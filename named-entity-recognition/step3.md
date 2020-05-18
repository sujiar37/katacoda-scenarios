We just made the clone to our application repository to the root directory and check that out,

`cd /root/NER-spacy ; ls`{{ execute }}

Take a look at both Dokerfile and Dockerfile_ML to understand the steps while building application and model images.

Now, let's build the application image which is written under the Dockerfile. We will follow the image name structure like this: *private_registry_address:port/image_name*

`docker build -t 127.0.0.1:30050/ner_app -f Dockerfile .`{{ execute }}

Wait for it to complete.

Ok, let's build the model image using the file Dockerfile_ML,

`docker build -t 127.0.0.1:30050/ner_model -f Dockerfile_ML .`{{ execute }}

verify the newy builded images are available under hosts,

`docker images | grep -i "ner_"`{{ execute }}

Finally push both images to our private registry

`docker push 127.0.0.1:30050/ner_app`{{ execute }}

`docker push 127.0.0.1:30050/ner_model`{{ execute }}

Cool !!! Let's go and deploy our application !!!