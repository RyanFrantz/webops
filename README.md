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
