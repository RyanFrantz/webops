# webops
Small web operations tools

## `storelog`

Accepts JSON strings representing Nginx access log entries on `stdin` and saves
them a `sqlite` database.

### Example Query

Querying the database for legitimate hits (i.e. not scanning requests) would
look similar to:

```terminal
sqlite> select
   ...> json_extract(json, '$.http_referer'),
   ...> json_extract(json, '$.request_uri')
   ...> from logs where legit=1;
https://ryanfrantz.com/|/hit/cv/
https://ryanfrantz.com/|/hit/
```

Total requests:

```terminal
sqlite> select count(*) from logs;
2262
```

NOTE: The above value is for all time (at least as long as the database has
existed). So many scanning requests.

Another way to identify legitimate requests:

```terminal
sqlite> select json_extract(json, '$.request_uri')
   ...> from logs
   ...> where json_extract(json, '$.http_referer') = 'https://ryanfrantz.com/';
/hit/cv/
/hit/
```

Raw counts by remote address:

```terminal
sqlite> select
   ...> json_extract(json, '$.remote_addr'),
   ...> count(*) as _count
   ...> from logs
   ...> group by json_extract(json, '$.remote_addr')
   ...> order by _count desc;
94.72.105.70|6594
92.255.57.58|2
43.159.152.184|2
87.121.79.3|1
87.120.125.13|1
34.22.192.129|1
193.34.212.75|1
```

Raw counts for legitimate requests to my site:

```terminal
sqlite> select
   ...> json_extract(json, '$.request_uri') as uri,
   ...> count(*) as _count
   ...> from logs
   ...> where legit=1
   ...> group by uri;
/hit/|1
/hit/cv/|1
```

NOTE: I will strip the leading `/hit` prefix when using this output in a report.

### Storing Logs Every Minute

The following `crontab` entry causes nginx access logs to be stored every
minute:

```terminal
* * * * * /usr/local/bin/logtail | /usr/local/bin/storelog > /var/log/ops/storelog 2>&1
```
