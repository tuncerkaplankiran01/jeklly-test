---
layout: post
title: AWS ECR Basic Automation
---

![docker](https://www.docker.com/sites/default/files/legal/small_v.png){:class="img-responsive"}

[AWS ECR](https://aws.amazon.com/ecr/) is a private docker registry provided by
Amazon. It is a good alternative to host your own repo server with
[Docker Registry](https://github.com/docker/distribution), or using
[the Docker Hub](https://hub.docker.com/) similar services especially if you
use AWS as your deployment environment. 
 
You can use images in repo directly in [AWS ECS](https://aws.amazon.com/ecs/)
service or even if you use plain EC2 containers updating docker container 
images in same region has no network cost.

You can use IAM user roles to give full access to CI user in your build 
environment and give read only access to docker hosts.

However contrary to Docker Hub, login to private repository ECR requires additional
steps. You need to use IAM user credentials with AWS CLI (you can also do 
the same with SDK) and acquire a temporary token to docker repo. Moreover, this
authentication sometimes fails and you may need to try a few times. This
step is necessary at the end of building image and also for pulling images
to docker hosts. 

To handle this task and autmate docker machine build with versioning I wrote
a couple of small [scripts](https://github.com/ozgurbtr/aws-ecr-scripts). It allows you
to build and automatically tag and push your docker images on AWS ECR repository
easily. It creates a latest tag and a version tag for latest builded image. It
is fine for my use case but beware using latest tag for deployment is deemed
[bad practice](https://medium.com/@mccode/the-misunderstood-docker-tag-latest-af3babfd6375#.nuz0sdhpn)
by some probably for good reason.

Script Requirements:

- You need to create an ECR repo. It is under ECS service on AWS console you should
select I will use repo option. 
- Requires AWS CLI
- AWS CLI profile named docker-manager for an IAM user with credentials for full access to ECR repo
- Update config.sh
- Run build-tag-latest.sh to build and push your new image
- Run delete-untagged.sh to remove old empty tags


