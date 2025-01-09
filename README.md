# webops
Small web operations tools

## `storelog`

Accepts JSON strings representing Nginx access log entries on `stdin` and saves
them a `sqlite` database.

### Example Query

Querying the database for legitimate hits (i.e. not scanning requests) would
look similar to:

```terminal
sqlite> select epoch, json_extract(json, '$.request_uri'),
   ...> json_extract(json, '$.host') from logs where legit=1;
1736452039|/hit/posts/calculating-disk-iops.html|assets.ryanfrantz.com
1736452039|/hit/posts/calculating-disk-iops.html|assets.ryanfrantz.com
```
