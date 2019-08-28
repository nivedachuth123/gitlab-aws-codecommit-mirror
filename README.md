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

_If you want to sync to a `ssh` enabled AWS Codecommit repo, use the following variant `.gitlab-ci.yml`_
```
image: rtcamp/gitlab-aws-codecommit-mirror
stages:
  - deploy

deploy to production:
  tags:
    - codecommit
  stage: deploy
  script:
    - bash setup_ssh
    - git clone --mirror $SOURCE_REPO rtSync
    - cd rtSync
    - git branch -a
    - git push --mirror $SCHEME://$REPO_URL
```
_If you want to sync to a `https` enabled AWS Codecommit repo, use the following variant `.gitlab-ci.yml`_
```
image: rtcamp/gitlab-aws-codecommit-mirror
stages:
  - deploy

deploy to production:
  tags:
    - codecommit
  stage: deploy
  script:
    - git clone --mirror $SOURCE_REPO rtSync
    - cd rtSync
    - git branch -a
    - git push --mirror $SCHEME://$USERNAME:$SECRET@$REPO_URL
```

* change tags from `codecommit` to tag of your newly created Gitlab CI Runner.

### Setting up variables

* Create the following Secret variables under Gitlab repository settings -> pipeline settings.
```
SCHEME	    Either 'https' or 'ssh' (without quotes)
REPO_URL    url of the AWS Code commit.(git-codecommit.us-east-1.amazonaws.com/v1/repos/MyDemoRepo)
SOURCE_REPO url of the source repo, it can be found on the project page of the Gitlab repo(Use ssh url of the repo). 
```
* If you use `https` authentication for AWS Codecommit add the following variables.
```
USERNAME AWS codecommit username
SECRET	 AWS codecommit password
```
* If you use `ssh` authentication for AWS Codecommit, add the following variable.
```
SSH_PRIVATE_KEY Private key file content that is authenticated for AWS Codecommit.
```
* If you are using `ssh` authentication. add `setup_ssh` file from this repo to your Gitlab repo.

![pipelines_ _secret-variables](https://user-images.githubusercontent.com/1140051/28919881-13c10aac-786d-11e7-99cb-a1ee9759ad8e.png)
* Commit the `.gitlab-ci.yml` and other required files to see Gitlab CI Runner send the commits to AWS codecommit repo.

### Does this interest you?

<a href="https://rtcamp.com/"><img src="https://rtcamp.com/wp-content/uploads/2019/04/github-banner@2x.png" alt="Join us at rtCamp, we specialize in providing high performance enterprise WordPress solutions"></a>
