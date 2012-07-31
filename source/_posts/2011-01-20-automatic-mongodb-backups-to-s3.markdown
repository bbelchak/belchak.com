---
comments: true
date: 2011-01-20 08:00:46
layout: post
slug: automatic-mongodb-backups-to-s3
title: Automatic MongoDB Backups to S3
wordpress_id: 10
categories:
- MongoDB
- Technology
tags:
- backups
- mongodb
- python
---

One of the big problems with hosting your own database solution is that you have to do backups for it on a regular basis. Not only do you need to do backups for it, but you need to also keep backups offsite. Luckily, [Amazon S3](http://aws.amazon.com/s3/) allows a cheap and easy solution for your offsite backups.

I found a shell [script solution](https://github.com/micahwedemeyer/automongobackup) for handling MongoDB backups, but it only does local backups. It keeps a nice history of recent backups, and rotates off the oldest ones when the threshold for age is reached. I modified the code to call a Python script that then synchronizes the newly created backup file to S3. I haven't wired up any purging functionality yet, and I don't know if I am going to. S3 storage is so cheap that it really doesn't matter much. A complete solution would, of course, keep your local files and your remote off-site backups in S3 in sync, but there is also a case to be made for keeping a rich history of backups in the "cloud" so as to be able to revert to any point in history if necessary.

The script that does the magic to synchronize and purge old backups is written in Python, and uses the [boto](https://github.com/boto/boto) library to quickly do the work.




    
    ACCESS_KEY='YOUR_ACCESS_KEY'
    SECRET='YOUR_SECRET_KEY'
    BUCKET_NAME='YOUR_BACKUPS_BUCKET' #note that you need to create this bucket first
    
    from boto.s3.connection import S3Connection
    from boto.s3.key import Key
    
    def save_file_in_s3(filename):
        conn = S3Connection(ACCESS_KEY, SECRET)
        bucket = conn.get_bucket(BUCKET_NAME)
        k = Key(bucket)
        k.key = filename
        k.set_contents_from_filename(filename)
    
    def get_file_from_s3(filename):
        conn = S3Connection(ACCESS_KEY, SECRET)
        bucket = conn.get_bucket(BUCKET_NAME)
        k = Key(bucket)
        k.key = filename
        k.get_contents_to_filename(filename)
    
    def list_backup_in_s3():
        conn = S3Connection(ACCESS_KEY, SECRET)
        bucket = conn.get_bucket(BUCKET_NAME)
        for i, key in enumerate(bucket.get_all_keys()):
            print "[%s] %s" % (i, key.name)
    
    def delete_all_backups():
        #FIXME: validate filename exists
        conn = S3Connection(ACCESS_KEY, SECRET)
        bucket = conn.get_bucket(BUCKET_NAME)
        for i, key in enumerate(bucket.get_all_keys()):
            print "deleting %s" % (key.name)
            key.delete()
    
    if __name__ == '__main__':
        import sys
        if len(sys.argv) < 3:
            print 'Usage: %s  ' % (sys.argv[0])
        else:
            if sys.argv[1] == 'set':
                save_file_in_s3(sys.argv[2])
            elif sys.argv[1] == 'get':
                get_file_from_s3(sys.argv[2])
            elif sys.argv[1] == 'list':
                list_backup_in_s3()
            elif sys.argv[1] == 'delete':
                delete_all_backups()
            else:
                print 'Usage: %s  ' % (sys.argv[0])





There is obviously a lot more work to be done on this script, but it's a start.

The appropriate setup for using this script and the AutoMongoBackup utility is to create a slave MongoDB node that receives synchronizations from the master. If you can handle having your Mongo instance locked for reads/writes while a backup is performed (i.e. you have a small database that backs up quickly) then you more than likely do not need to do the slave method.

Anyway, hope this helps! I'd love to hear other ideas about how else this can be done.
