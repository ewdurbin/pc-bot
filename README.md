### Tools for PyCon-PC IRC meetings

Helpful tools for running the PyCon Program Commitee process. This runs
an IRC bot we use during meetings, and a web interface to the review data.

#### Setup

First, the usual:

    pip install -r requirements.txt

Then, the best way to configure this thing is through some environ variables
(there are flags, but env is easier):

    export PYCON_API_KEY='00000000-0000-0000-0000-000000000000'
    export PYCON_API_SECRET='00000000-0000-0000-0000-000000000000'
    export PYCON_WEBSITE_HOST='staging-pycon.python.org'
    export PYCONBOT_SUPERUSERS=list,of,irc,superusers

The web app can be run in debug mode:

    export PYCONBOT_DEBUG=1

Then to run the bot:

    python runbot.py

The script needs to be able to import stuff from `pycon_bot`, so I usually:

    export PYTHONPATH=`pwd`
    python scripts/agenda.py

Or somesuch.


#### Architecture 

Some brief notes on how all the bits fit together:

  * `runbot.py` launches an IRC "driver" (`pycon_bot.driver.PyConBot`). This
    bot doesn't do much itself besides provide some utilities. Instead,
    almost all bot commands get delegated down to a "mode" object.
  * A "mode" lives in `pycon_bot.modes.<whatever>` It should subclass
    `pycon_bot.modes.base.BaseMode`, and the mode class itself is named `Node`.
  * The ",mode" command switches between modes - see
    `pycon_bot.driver.PyConBot.handle_mode`. Giving invalid modes fails in
    predictable ways, and since it imports arbitrary code you probably want to
    be careful about where you run this and who gets SU!


#### Running IN THE CLOUDS!

You can run this bad boy on Heroku:

    heroku create <appname>
    git push heroku master
    heroku config:set PYCONBOT_SUPERUSERS=me,myself,andI
    heroku config:set PYCONBOT_NICK='pycon_bot'
    heroku config:set PYCON_API_KEY='00000000-0000-0000-0000-000000000000'
    heroku config:set PYCON_API_SECRET='00000000-0000-0000-0000-000000000000'
    heroku config:set PYCON_WEBSITE_HOST='us.pycon.org'

Then to change code just do the standard `git push heroku master`.
