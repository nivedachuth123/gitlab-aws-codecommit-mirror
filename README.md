# Gitlab AWS Codecommit Mirror
Use AWS codecommit as a mirror for self hosted gitlab repo. This could come in handy if you have your production linked with codecommit, or you just want to backup your branches of your self hosted gitlab to AWS.


### prerequisite
* Docker installed in your local machine. [Installation guide.](https://docs.docker.com/engine/installation/)
* [Docker hub account.](https://hub.docker.com)
* Gitlab CI working.[Docs](https://about.gitlab.com/gitlab-ci/)
* How to create Gitlab CI runners. [Docs](https://docs.gitlab.com/ee/ci/runners/README.html)

### How to use
* Create a codecommit repo from AWS console. Find docs [here](http://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-gc.html).

* Clone this repo.
```
git clone  https://github.com/rtCamp/gitlab-aws-codecommit-mirror.git
```

* Build docker image for runner.
```
docker build . -t <docker_hub_username>/gitlab-aws-codecommit-mirror
```
For example 
```
docker build . -t rtcamp/gitlab-aws-codecommit-mirror
```
And push the newly built image to your [Docker Hub Account](https://hub.docker.com).

Or you can use our image at `rtcamp/gitlab-aws-codecommit-mirror`

* Register a new Gitlab CI runner with this docker image.

* Clone your existing gitlab repo
* Copy `.gitlab-ci.yml` from this repo to your repo.
```
image: rtcamp/gitlab-aws-codecommit-mirror
stages:
  - deploy

deploy to production:
  tags:
    - codecommit
  stage: deploy
  script: 
    - git remote set-url origin $SCHEME://$USERNAME:$SECRET@$REPO_URL
    - git push origin HEAD:refs/heads/$CI_COMMIT_REF_NAME --force
  only:
    - master
    - develop
```
_NOTE: change tags from `codecommit` to tag of your newly created Gitlab CI Runner._
_Note: As this searves as a mirror, it just replicates master and develop branches, and will not delete branches from AWS codecommit._
* Create the following Secret variables under Gitlab repository settings -> pipeline settings.
```
REPO_URL url of the AWS Code commit.(git-codecommit.us-east-1.amazonaws.com/v1/repos/MyDemoRepo)
SCHEME	 currently this suports only `https`.
USERNAME AWS codecommit username
SECRET	 AWS codecommit password
```
![pipelines_ _secret-variables](https://user-images.githubusercontent.com/1140051/28919881-13c10aac-786d-11e7-99cb-a1ee9759ad8e.png)
* Commit the `.gitlab-ci.yml` to see Gitlab CI Runner send the commits to AWS codecommit repo.
