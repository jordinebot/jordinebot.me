---
title: "How to Deploy Ghost 1.x to Heroku"
date: 2018-05-07T07:39:40+02:00
draft: true
---

When I decided to use Ghost as my new blogging platform I explored some hosting alternatives...

One of them, even after I discarted it, became quite a challenge, so I decided to write a post about it.

First of all, I could find [some good posts](http://www.autodidacts.io/host-a-ghost-blog-on-heroku/) about deploying
Ghost versions up to 0.11.x to Heroku that worked like a charm. But, if you try to deploy Ghost 1.0 or newer there are a
couple of things you need to know and have in mind to get it running on Heroku.

1. Ghost's team decided to [drop support for PostgreSQL](https://dev.ghost.org/dropping-support-for-postgresql/) on Ghost 1.0
2. Ghost 1.0 [introduced `nconf` for configuration](https://dev.ghost.org/nconf/).

So, let's see how to deploy the latest Ghost (that's currently 1.5.0) to Heroku step by step while learning to overcome those caveats.

## 1. Download and unzip the latest package.

Go to [Ghost's Github releases tab](https://github.com/TryGhost/Ghost/releases) and download the latest 1.x release. **Caution:** It's the zipfile named `Ghost-1.x.x.zip`. Not the source code.

Unzip the file wherever you want on your local machine.

## 2. Init a repo on that folder.

You'll need a git repo to push your Ghost to Heroku, so change dir to your local Ghost folder (let's assume it's `~/Projects/Ghost-1.5.0/`)

    cd ~/Projects/Ghost-1.5.0
    
    git init
    
It's a good idea to gitignore the commonly ignored files. At least,

    echo "node_modules" > .gitignore
    
## 3. Using `npm` or `yarn`, install the project's dependencies.

I am assuming you have Node.js `>= 6` (and therefore NPM `>=3.10`) and/or yarn `>= 0.27` properly installed in your system. So, just run:

    npm install
    
or

    yarn install
    
## 4. Login to Heroku-CLI and create an app

For this step you'll need the [Heroku Command Line Interface](https://devcenter.heroku.com/articles/heroku-cli) (aka Heroku Toolbelt)

    heroku login
    heroku create <app_name> --buildpack https://github.com/heroku/heroku-buildpack-nodejs.git
    
This `<app_name>` will be part of your app's default URL `https://app_name.herokuapp.com/` so it has to be unique in Heroku's namespace.

If you don't know what to choose or you plan to use a custom domain for your app, just type `heroku create --buildpack https…` and let the platform generate a random app name for you.

The `--buildpack` argument will setup your dyno to automate the build process for Node. Check Heroku's Dev Center to learn more about available [Buildpacks](https://elements.heroku.com/buildpacks).

If you already created your app without a buildsystem, you can set it later with:

    heroku buildpacks:set https://github.com/heroku/heroku-buildpack-nodejs -a <app_name>

Check that your `fetch` and `push` remote URL's have been set.

    git remote -v
    
They should be set if you created the local repo on step 2. If for any reason they're not, just add them with:

    heroku git:remote -a <app_name>
    
## 5. Allocate a DB resource for your app

Maybe the easiest way to do this is through Heroku's Dashboard, as it gives you the option to explore the available alternatives. Go to your app's resources tab and search for MySQL databases:

![heroku-resources-tab](/content/images/2017/08/heroku-resources-tab.jpg)

The current choices are [ClearDB MySQL](https://elements.heroku.com/addons/cleardb) and [JawsDB MySQL](https://elements.heroku.com/addons/jawsdb). You can compare them and their plans & prices in the [Elements Marketplace](https://elements.heroku.com/).

If you already knew which add-on and plan you want for your app, you could just type:

    heroku addons:create jawsdb:kitefin
    
where `jawsdb` is the add-on and `kitefin` the plan.

## 6. Set up the database credentials as `env` variables.

In earlier versions of Ghost, the config file was `config.js`. That's a Javascript file, so it was possible to just use Heroku's `process.env.VARIABLES` in it, like:

    database: {
        client: 'postgres',
        connection: {
            host: process.env.POSTGRES_HOST,
            user: process.env.POSTGRES_USER,
            password: process.env.POSTGRES_PASSWORD,
            database: process.env.POSTGRES_DATABASE,
            port: '5432'
        }
    },
    
But since they adopted [`nconf`](https://github.com/indexzero/nconf) the config files are now JSON files (e.g. `config.production.json`) and therefore variables aren't allowed in them.

After some digging into the `nconf`'s [Hierarchical Configuration](https://github.com/indexzero/nconf#hierarchical-configuration) and a little help from my friend the Rubber Duck in Stack Overflow. (See [How to read Heroku's nested process.env vars / object in nconf?](https://stackoverflow.com/questions/45434620/how-to-read-herokus-nested-process-env-vars-object-in-nconf)) I figured out how to reproduce this database's config object with Heroku's `env` variables.

So, in Heroku's Dashboard's Resource tab, click into your DB add-on to reveal your Database credentials and set your `env` variables as:

![heroku_db_credentials](/content/images/2017/08/heroku_db_credentials.jpg)

    heroku config:set database__client=mysql
    heroku config:set database__connection__host=<YOUR_DB_HOST>
    heroku config:set database__connection__user=<YOUR_DB_USERNAME>
    heroku config:set database__connection__password=<YOUR_DB_PASSWORD>
    heroku config:set database__connection__database=<YOUR_DB_NAME>
    heroku config:set database__connection__port=<YOUR_DB_PORT>
    
## 7. Set up the app's URL in Ghost config.

Edit your `config.production.json` and add a new key to the JSON as:

    "url": "https://your_app_name.herokuapp.com/",
    
Save it.

## 8. Replace the server settings.

In the default `config.production.json` file, server settings are:

    "server" : {
        "host" : "127.0.0.1",
        "port" : "2368"
    }
    
We need to set the host to `0.0.0.0` (and that could be done like we already did in step 6 for DB credentials, so no big deal). But:

*On Heroku, apps are completely self-contained and do not rely on runtime injection of a webserver into the execution environment to create a web-facing service. Each web process simply binds to a port, and listens for requests coming in on that port. The port to bind to is assigned by Heroku as the PORT environment variable.* (See [Runtime Principles > Web Servers](https://devcenter.heroku.com/articles/runtime-principles#web-servers))

So, here's the tricky thing:

1. We cannot set `process.env.PORT` directly into the `config.production.json` (as we've seen, it doesn't allow vars)

2. We cannot set a `server__port` variable because we don't know which port will Heroku assign to our web process.

### Proposed workaround:

Edit `core/server/config/index.js` with your favourite editor and search for `return nconf` (it should be [line 75](https://github.com/TryGhost/Ghost/blob/master/core/server/config/index.js#L75)).

Just before this statement, add:

    var server = nconf.get('server');
    server.host = "0.0.0.0";
    server.port = process.env.PORT;
    nconf.set('server', server);
    
## 9. Add a Procfile that will launch your Ghost in production.

    echo "web: NODE_ENV=production node index.js" > Procfile
    
## 10. Deploy Ghost application

    git add --all
    git commit -m "Configured Ghost for Heroku + MySQL"
    git push heroku master
    
## 11. Create the production database scheme.

For this step you need to SSH into your Heroku app. Fortunately, Heroku-CLI makes this really easy. Just type:

    heroku run bash
    
And you're in! Now install `knex-migrator`:

    npm install -g knex-migrator
    
And run:

    knex-migrator init
    
You should see an output like:

    INFO Creating table: posts
    INFO Creating table: users
    INFO Creating table: roles
    INFO Creating table: roles_users
    INFO Creating table: permissions
    INFO Creating table: permissions_users
    INFO Creating table: permissions_roles
    INFO Creating table: permissions_apps
    INFO Creating table: settings
    INFO Creating table: tags
    INFO Creating table: posts_tags
    INFO Creating table: apps
    INFO Creating table: app_settings
    INFO Creating table: app_fields
    INFO Creating table: clients
    INFO Creating table: client_trusted_domains
    INFO Creating table: accesstokens
    INFO Creating table: refreshtokens
    INFO Creating table: subscribers
    INFO Creating table: invites
    INFO Creating table: brute
    INFO Model: Post
    INFO Model: Tag
    INFO Model: Client
    INFO Model: Role
    INFO Model: Permission
    INFO Model: User
    INFO Relation: Role to Permission
    INFO Relation: Post to Tag
    INFO Relation: User to Role
    INFO Finished database init!
    
After that, remember to `exit` to return your session to the local machine ;)

## 12. Restart your dyno(s)

    heroku restart
    
And we're done! Enjoy Ghost!



