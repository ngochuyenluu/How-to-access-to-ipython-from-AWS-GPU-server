# How-to-access-to-ipython-from-AWS-GPU-server
From my experience, setting up the environment for AWS GPU Deep Learning is quite hard and time consuming cause it depends on many dependencies and version of modules. Today I will give a shortlist of code to access AWS GPU from Jupyter notebook. Of course, it was designed to run on Python, but many machine learners are familiar with Jupyter due to its clearly understanding and conveniences. There are severals guidelines to do that but unfortunately I did not success to follow so I decided to write this post to help me and other people from time consuming in a fastest way cause our life is so short.

Firstly, you need to create an account or sign in to your Amazon account, click EC2 to choose which enviroment you would like to work with: 

![Image1](https://hackernoon.com/keras-with-gpu-on-amazon-ec2-a-step-by-step-instruction-4f90364e49ac)

With me, because I need GPU for some Kaggle Deep learning challenges so definitely I will choose Deep Learning AMI Ubuntu, or you can choose version Amazon Linux just below to work with, they have the same performances just have some different code to access that I will show you later.

![Image2](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2017/09/13/get-started-dl-3.gif)

Then choose GPU compute p2.xlarge to continue, it will cost you 0.9$/hour to use so that’s why I write this resume to help people save a little bit time and money. You should participate in some hackathons to obtain the AWS credits.

![Image3](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2017/09/13/get-started-dl-4.gif)

Click “Edit security groups” and add rules to our instances, in particular you need to specify the port range of custom TCP which is used when we access to Jupyter port. You can choose 8888 or change, just do not use the number port is already running.

![Image5](http://blog.impiyush.com/2015/02/running-ipython-notebook-server-on-aws.html)

Then the system will ask you to select the key pair. You can create for first time or reuse the key pair if you already have it. Just remember the key pair should be unique from the pool of names out there. Save the key pair .pem to your directory for example downloads.

![Image6](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2017/09/13/get-started-dl-7.gif)

Ok let’s lunch the instance. Pay attention to your public DNS cause we need it to access to our GPU.

![Image7](https://d2908q01vomqb2.cloudfront.net/f1f836cb4ea6efb2a0b1b99f41ad8b103eff4b59/2017/09/13/get-started-dl-9.gif)

Then the part without image starts, open you terminal. Firstly we need to cd in the directory of your key pair and access to our AWS server. For example:

cd downloads

ssh -i <name.pem> ubuntu@<public DNS> #replace the name of your key pair to the first one and number of public DNS that we mentioned above to the second.

Continue to expect the permission by typing yes and the server will run. 

![Image8](http://blog.impiyush.com/2015/02/running-ipython-notebook-server-on-aws.html)

Then start ipython by type ipython in terminal. Next step we need to extract the password to access to ipython by use and don’t forget to save our output because we will use it later.

from IPython.lib import passwd

passwd()

exit()

We need to create the certificate to our ipython access. 

mkdir ssl

cd ssl

sudo openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout "cert.key" -out "cert.pem" -batch

cd on terminal to come back the sceen and modify the jupyter_notebook_config.py by using vi. 

vi ~/.jupyter/jupyter_notebook_config.py

In fact you can write directly on this file but I don’t recommend it. If you are newbie with vi, don’t worry cause all the work in here is simple. Type i to modify and when you finish all the thing, type esc and :wq to quit and save our code. Ok let’s do it.

c = get_config()  # get the config object

c.NotebookApp.certfile = u'/home/ubuntu/ssl/cert.pem' # path to the certificate we generated

c.NotebookApp.keyfile = u'/home/ubuntu/ssl/cert.key' # path to the certificate key we generated

c.IPKernelApp.pylab = 'inline'  # in-line figure when using Matplotlib

c.NotebookApp.ip = '*'  # serve the notebooks locally

c.NotebookApp.open_browser = False  # do not open a browser window by default when using notebooks

c.NotebookApp.password = 'sha1:f7817a9bc4b4:f684c8f9e708bfcbb60e984373ba31296937aff0'  # this is the password hash that we generated earlier, replace your saved password here.

c.NotebookApp.port = 8888

Ok, we almost done. Let’s create a new directory for jupyter.

mkdir notebook

cd notebook

jupyter notebook

If all the steps are correct, the server will run and give you the link for example http://[your DNS]:8888, replace the number of AWS DNS I have rounded above and you will receive:

![Image9](http://blog.impiyush.com/2015/02/running-ipython-notebook-server-on-aws.html)

Don’t worry it’s normal. Click show details and go the file to access to our ipython server. If you have set up a password to your system then permit it to go ahead. If no, click log in to rock on. Congratulation :)


Of course the ipython on AWS server is different with yours so sometimes you need to install the essential modules at the first time. However, it depends on your target and problem to install, I will do another tutorial about how to challenge a Kaggle competition by using AWS server and talk about it later.
