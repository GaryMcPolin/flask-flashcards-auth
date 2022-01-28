
## Deploying a Flask Application
Use **Gunicorn**, a Python HTTP server. <br>
Gunicorn is meant to be a production-ready server, but it does have some limitations, so it's best to run it behind a so-called reverse proxy like **nginx**. 

### Environment Setup:
Ubuntu (based on Debian. Uses the **apt** package manager)<br>
1. Install **Python 3** (installed on Ubuntu by default)
2. Install **Python3-pip** so that we can install our dependencies. 
3. Install **Git** and **nginx**, the reverse proxy.

### Deploying Flask on Linux with Gunicorn
We're going to deploy our Flask app on Ubuntu with Gunicorn, and we'll run that behind a reverse proxy called nginx. 
1. Install code onto server
2. Install Guinicorn3 _(better to install globally - see note1 below)_
3. Create virtual environment 
4. Install dependencies with pip 
5. Run server using `gunicorn3 -D <module_name>:<flask_object>` _(see note2, note3)_

### Configure nginx:
1. Go to `/etc/nginx/sites-available`
2. Delete default site (nginx welcome page)
3. Follow the [deployment instructions](https://gunicorn.org/#deployment) on the Gunicorn website
4. Copy the example configuration for nginx into a new file on the server, called `default`. Edit values as needed
5. Restart nginx using `sudo service nginx restart`

We now have an nginx which accepts HTTP connections, it gives us better performance and security, and it forwards requests to Gunicorn, which runs our app. 

### Other Information:
There are a lot more ways in which you can run Gunicorn. Check out the [documentation](https://docs.gunicorn.org/en/stable/deploy.html).

There's also an option called **Monitoring**, and it talks about different ways to run your Gunicorn application. 
Most importantly, there are items here about **Upstart** and **Systemd**, which will allow your application to run as a Linux system service (you probably want that). 
Among other things, it will allow your website to start automatically after the system reboots. 

Also look at the [Flask deployment options](https://flask.palletsprojects.com/en/2.0.x/deploying/). 
**Hosted options** are ways to host your application without having to deal with system maintenance yourself. These services give you a completely installed and ready-to-go system. Usually you just upload your code, and that's it. 
Under the **self-hosted options**, we see Gunicorn, as well as some alternatives. 
<br>
#### Notes:
**_Note1:_** Install Guincorn3 using the Ubuntu package manager, apt, so that you will get security updates with your usual system maintenance updates. If you are planning to run multiple projects on multiple Gunicorn instances, doing everything inside virtual environments might be what you want to do. <br>
**_Note2:_** The part before the colon is my module name, e.g. `app.py`, but without the .py part. After the colon comes the name of the Flask object inside the module, which is app.  <br>
**_Note3:_** Gunicorn alone listens on port 8000. To actually run a real website, we need this to listen to port 80, but normal users don't have permission to do that. We might run the whole thing as a superuser to make it listen to port 80, but that's a bad idea. I don't want to run my code as a superuser. What if there's a bad bug or a security hole? We use `nginx`, which is actually a web server that does listen to port 80, and it can accept connections and then forward them to Gunicorn on port 8000. Nginx will also prevent some denial-of-service attacks that Gunicorn is vulnerable to. Running Gunicorn with the -D switch means it will run in the background as a daemon, and this also means the program will now keep running even after you log out. 
