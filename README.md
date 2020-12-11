# sample
Step -1

git clone https://github.com/dockersamples/node-bulletin-board
cd node-bulletin-board/bulletin-board-app

Step-2

Sample Dockefile

FROM node:current-slim
WORKDIR /usr/src/app
COPY package.json .
RUN npm install
EXPOSE 8080
CMD [ "npm", "start" ]

# Copy the rest of your app's source code from your host to your image filesystem.
COPY . .

Step-3

docker build --tag bulletinboard:1.0 .

Step-4

docker run --publish 8000:8080 --detach --name bb bulletinboard:1.0

Step-5

docker rm --force bb




Second Question Answer :

Step-1

build:
  docker:
    web: Dockerfile
run:
  web: bundle exec puma -C config/puma.rb

Step-2

git add Example.yml
git commit -m "Add Example.yml"

Step-3

Example stack:set container

Step-4

git push Eample master

Step-5

setup:
  addons:
    - plan: Example-postgresql
      as: DATABASE
  config:
    S3_BUCKET: my-example-bucket
build:
  docker:
    web: Dockerfile
    worker: worker/Dockerfile
  config:
    RAILS_ENV: development
    FOO: bar
release:
  command:
    - ./deployment-tasks.sh
  image: worker
run:
  web: bundle exec puma -C config/puma.rb
  worker: python myworker.py
  asset-syncer:
    command:
      - python asset-syncer.py
    image: worker

Step-5

setup:
  addons:
  - plan: Example-postgresql
    as: DATABASE

Step-6

setup:
  config:
    S3_BUCKET: my-sample-bucket

Step-7

build:
  docker:
    web: Dockerfile
    worker: worker/Dockerfile

Step-8

build:
  config:
    RAILS_ENV: development
    FOO: bar

Step-9

build:
  docker:
    release:
       dockerfile: Dockerfile
       target: builder
    web: Dockerfile

Step-10
 Configuring release phase
build:
  docker:
    web: Dockerfile
    worker: worker/Dockerfile
release:
  image: worker
  command:
    - ./deployment-tasks.sh

Step-11
Defining the processes to run
run:
  web: bundle exec puma -C config/puma.rb

Step 1. Create a Simple Node App

$ mkdir node-docker-ecs
$ cd node-docker-ecs

Step-2

$ npm init --y

Step-3

$ npm install express

Create an index.js file 

example:
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => res.send('Hello World!'))

app.listen(port, () => console.log(`Example app listening on port ${port}!`))

Step-4

The app can then run with this command:
$ node index.js

Step-5

We should see our app at http://localhost:3000/:

Step-6 Dockerize the Node App

Create a Dockerfile in your project directory and populate it with the

Example :

# Use an official Node runtime as a parent image
FROM node:12.7.0-alpine

# Set the working directory to /app
WORKDIR '/app'

# Copy package.json to the working directory
COPY package.json .

# Install any needed packages specified in package.json
RUN yarn

# Copying the rest of the code to the working directory
COPY . .

# Make port 3000 available to the world outside this container
EXPOSE 3000

# Run index.js when the container launches
CMD ["node", "index.js"]

Step :7Push the Node App to AWS ECR
Now it is time we pushed our container to a container registry service — in this case,
 we will use AWS ECR:

Step:7

Instead of using the AWS UI, we will use terraform to create our repository. 
In our directory create a file called main.tf.

For Example:
provider "aws" {
  version = "~> 2.0"
  region  = "eu-west-2" # Setting my region to London. Use your own region here
}

resource "aws_ecr_repository" "my_first_ecr_repo" {
  name = "my-first-ecr-repo" # Naming my repository
}
Step:8

Next,in your terminal, type:

terraform init
terraform apply

Step 9. Create the Cluster
Navigate to the AWS ECS service and click Clusters.

add sample code to your terraform file and redeploy your infrastructure with terraform apply:

Example:
resource "aws_ecs_cluster" "my_cluster" {
  name = "my-cluster" # Naming the cluster
}

Step 10. Now we can Create the First Task

For Example :

resource "aws_ecs_task_definition" "my_first_task" {
  family                   = "my-first-task" # Naming our first task
  container_definitions    = <<DEFINITION
  [
    {
      "name": "my-first-task",
      "image": "${aws_ecr_repository.my_first_ecr_repo.repository_url}",
      "essential": true,
      "portMappings": [
        {
          "containerPort": 3000,
          "hostPort": 3000
        }
      ],
      "memory": 512,
      "cpu": 256
    }
  ]
  DEFINITION
  requires_compatibilities = ["FARGATE"] # Stating that we are using ECS Fargate
  network_mode             = "awsvpc"    # Using awsvpc as our network mode as this is required for Fargate
  memory                   = 512         # Specifying the memory our container requires
  cpu                      = 256         # Specifying the CPU our container requires
  execution_role_arn       = "${aws_iam_role.ecsTaskExecutionRole.arn}"
}

resource "aws_iam_role" "ecsTaskExecutionRole" {
  name               = "ecsTaskExecutionRole"
  assume_role_policy = "${data.aws_iam_policy_document.assume_role_policy.json}"
}

data "aws_iam_policy_document" "assume_role_policy" {
  statement {
    actions = ["sts:AssumeRole"]

    principals {
      type        = "Service"
      identifiers = ["ecs-tasks.amazonaws.com"]
    }
  }
}

resource "aws_iam_role_policy_attachment" "ecsTaskExecutionRole_policy" {
  role       = "${aws_iam_role.ecsTaskExecutionRole.name}"
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy"
} 







