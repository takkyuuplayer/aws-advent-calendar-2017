# [Amazon Route 53](https://aws.amazon.com/route53/?nc1=h_ls)

This is 2nd entry of my advent calendar in 2017.

## Motivation

Let's migrate DNS records registered on [ムームードメイン](https://muumuu-domain.com/) to Route 53. I have 3 things to migrate.

1. `MX` records for [G Suite](https://gsuite.google.com/)

  ```
  $ dig takkyuuplayer.com any

  ;; ANSWER SECTION:
  takkyuuplayer.com.      3578    IN      NS      dns01.muumuu-domain.com.
  takkyuuplayer.com.      3578    IN      SOA     takkyuuplayer.com. postmaster.takkyuuplayer.com. 1512155811 3600 1800 604800 3600
  takkyuuplayer.com.      3100    IN      MX      30 aspmx5.googlemail.com.
  takkyuuplayer.com.      3100    IN      MX      30 aspmx4.googlemail.com.
  takkyuuplayer.com.      3100    IN      MX      30 aspmx3.googlemail.com.
  takkyuuplayer.com.      3100    IN      MX      20 alt2.aspmx.l.google.com.
  takkyuuplayer.com.      3100    IN      MX      20 alt1.aspmx.l.google.com.
  takkyuuplayer.com.      3100    IN      MX      30 aspmx2.googlemail.com.
  takkyuuplayer.com.      3100    IN      MX      10 aspmx.l.google.com.

  ;; ADDITIONAL SECTION:
  aspmx.l.google.com.     271     IN      A       173.194.68.27
  ```

1. `CNAME` record for blog.takkyuuplayer.com

  ```
  -> $ dig blog.takkyuuplayer.com any

  ;; ANSWER SECTION:
  blog.takkyuuplayer.com. 3599    IN      CNAME   ghs.google.com.

  ```

1. `A` record for www.takkyuuplayer.com

  ```
  -> $ dig www.takkyuuplayer.com any

  ;; ANSWER SECTION:
  www.takkyuuplayer.com.  3599    IN      A       153.126.171.12
  ```

## Let's use it

Create a new Hosted Zone from [Route 53 Management Console](https://console.aws.amazon.com/route53/home#hosted-zones:) with domain name `takkyuuplayer.com` and create records sets in it.

1. For G Suite, [Amazon Web Services: Set up G Suite MX records \- G Suite Administrator Help](https://support.google.com/a/answer/6149697?hl=en) is helpful.
1. For `blog.takkyuuplayer.com`, I already moved out from Blogger to [Hatena Blog](http://hatenablog.com/) so pointing CNAME to Blogger is out of date. Let's delete.
1. For `www.takkyuuplayer.com`, set the same `A` record to Route 53.

At last, configure [ムームードメイン](https://muumuu-domain.com/) to use Route 53's name server. Belows are the result.

```
-> $ dig takkyuuplayer.com any

;; ANSWER SECTION:
takkyuuplayer.com.      21599   IN      NS      ns-1501.awsdns-59.org.
takkyuuplayer.com.      21599   IN      NS      ns-1689.awsdns-19.co.uk.
takkyuuplayer.com.      21599   IN      NS      ns-223.awsdns-27.com.
takkyuuplayer.com.      21599   IN      NS      ns-552.awsdns-05.net.
takkyuuplayer.com.      899     IN      SOA     ns-223.awsdns-27.com. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400
takkyuuplayer.com.      3599    IN      MX      1 aspmx.l.google.com.
takkyuuplayer.com.      3599    IN      MX      10 alt3.aspmx.l.google.com.
takkyuuplayer.com.      3599    IN      MX      10 alt4.aspmx.l.google.com.
takkyuuplayer.com.      3599    IN      MX      5 alt1.aspmx.l.google.com.
takkyuuplayer.com.      3599    IN      MX      5 alt2.aspmx.l.google.com.

-> $ dig www.takkyuuplayer.com any

;; ANSWER SECTION:
www.takkyuuplayer.com.  299     IN      A       153.126.171.12
```

Done.

## Accident

After deleting `blog.takkyuuplayer.com`, my homepage started to return 500 error because of failure of fetching atom feed.

> [Fix blogger url by takkyuuplayer · Pull Request \#4 · takkyuuplayer/homepage](https://github.com/takkyuuplayer/homepage/pull/4/files#diff-0a400c8217d53ff4f978163d7c61868cL14)

It is very bad behavior that my website depends on availability of external websites.

## Conclusion

LGTM to use Route 53.

## Prior Notice

Tomorrow entry is S3.
