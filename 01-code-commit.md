# [AWS CodeCommit](http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-create-repository.html)

This is 1st entry of my advent calendar in 2017.

## Motivation

It is not good idea to save sensitive information on GitHub even in a private repository. They can be seen not only developers but also all members who can access to the repository. GitHub forbid us to push such information to remote.

> Warning: Never git add, commit, or push sensitive information to a remote repository.
>
> https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/

Where should we save those data? I usually use local git's bare repository but CodeCommit might be an another option.

## Let's use it

Let's create a repository for sensitive information first.
I made a repository named `DemoSensitive` from `Get Started` link on [AWS CodeCommit Management Console](https://ap-northeast-1.console.aws.amazon.com/codecommit/home?region=ap-northeast-1#/introduction). When I created the repository, I was redirected to a page to configure email notification.

Whenever we access to AWS services, best practice is using IAM user with limited permissions. I created a new account `codecommit-rw` for the purpose by following

> [Setup Steps for SSH Connections to AWS CodeCommit Repositories on Linux, macOS, or Unix \- AWS CodeCommit](http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html)

Now ready to `git clone` the repository. I add, commit and push dummy sensitive data. I added it as `submodules/DemoSensitive` in this advent calendar repository.

## Consideration

### How secure is it compared to GitHub?

Using CodeCommit means we push sensitive information to **remote**, which is not allowed on GitHub. Below are their security policies.

* [AWS CodeCommit Security](https://aws.amazon.com/codecommit/faqs/?nc1=h_ls)
* [GitHub Security](https://help.github.com/articles/github-security/)

AWS encrypt our repository while GitHub doesn't. However, I agree with the Github's policy so it doesn't matter for me.

> We do not encrypt repositories on disk because it would not be any more secure: the website and git back-end would need to decrypt the repositories on demand, slowing down response times. Any user with shell access to the file system would have access to the decryption routine, thus negating any security it provides. Therefore, we focus on making our machines and network as secure as possible.

The biggest difference must be existence of IAM user. We can control the available operations to the repository, or limit access by IP address.

### How secure is it compared to local git?

As long as we do team development, we need somewhere to share sensitive information. It might be git's bare repository in an EC2 instance, a gitlab server in our office, file server, google docs, ...etc. Any case the critical things for security are

* Only limited people can access
* From specific location

which seem to be accomplished by CodeCommit with IAM user.

## Prior Notice

Tomorrow, I'll try Route53.
