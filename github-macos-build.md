# Connecting Github Actions to a Physical Mac

Our continuous integration setup builds iOS apps for the app store based on Github pushes.

Github actions are a great product which makes CI easy to setup.

There's just one catch: the MacOS machines cost 10x Linux machines, starting at $0.08 per minute.

That means for one machine year, you are paying 365 x 24 x 60 x 0.08 = $42,048! This is the price of 70 brand new Mac minis.

If you are maintaining just 3 apps, with a new build every day at 25 minutes per build, this will still cost you 3 x 25 x 365 x 0.08 = $2,190 per year!

At the moment we deploy about 50 apps, on a weekly basis with 2-3 builds per app per week (for demos as well as production). This setup alone gets us to 50 x 3 x 52 x 25 x 0.08 = $15,600.

Not only is this an obnoxious expense, it should be every team's endeavour to encourage faster development cycles and more frequent build/test cycles.

Needless to say, we very quickly started to take advantage of Github's "self-hosted" actions, and used the extra budget to purchase a few new Mac minis.

The following is our internal guide for how to do this.

# High level takeaways

1. Don't forget to read Github's guide on the actions process: https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/add-runners
2. Use the command `caffeinate -s` to keep Mac machines awake
3. For MacBooks, faking a display is a good way to keep it alive

# Step by step guide

1. Set up a new Mac, do not use iCloud, do not set up Siri or other services
1. Create an SSH key and link it with your Github account
1. Check out your working repo
1. Get Homebrew - https://brew.sh
1. Get `xcodes`: this manages Xcode versions - https://github.com/XcodesOrg/xcodes
    ```bash
    brew install xcodesorg/made/xcodes
    
    # List xcodes
    xcodes list
    
    # Install the latest Xcode
    xcodes install 15.2.0
    
    # Still need to install iOS or other runtimes
    # xcodes runtimes --include-betas
    # For example
    # xcodes runtimes install "iOS 17.2"
    ```

1. Get `fastlane`: iOS build tool

    ```bash
    brew install fastlane
    ```

1. Install Ruby and update

    ```bash
    brew install chruby ruby-install
    
    # specify a ruby version (2.7.8 seems to work well as of Feb, 2024)
    # ruby 3.3.0 breaks some kind of File.exists/File.exist API
    ruby-install ruby 2.7.8
    
    # set up chruby in PATH
    echo "source $(brew --prefix)/opt/chruby/share/chruby/chruby.sh" >> ~/.zprofile
    echo "source $(brew --prefix)/opt/chruby/share/chruby/auto.sh" >> ~/.zprofile
    echo "chruby ruby-2.7.8" >> ~/.zprofile
    ```

1. Install cocoapods

    ```bash
    gem install cocoapods
    ```

1. Install Github self-hosted runners
1. Turn Rosetta on (seems some libraries use this)

    ```bash
    softwareupdate --install-rosetta
    ```

1. Recommended: install Tailscale
1. Recommended: enable screen share and SSH, so you can login to this computer remotely

    - Turn on Screen Sharing in the settings app
    - Turn on Remote Login in the settings app

1. Extra: if the build machine is a MacBook

    - Set up the computer to stay awake
    - Change settings in `System Settings` > `Lock Screen`
    - Stay awake even when the lid is closed
        ```bash
        caffinate -s
        ```
    - Helpful tip: buy a dongle, search Amazon for "HDMI dummy plug" or "USB-C display dummy plug"

A personal note. I came of age (from an engineering mastery perspective) during an age where cloud computing was all the rage. In fact, today, cloud has gone to containerized applications to server-less functions running on CDN edges (or whatever the accurate terminology to describe whatever https://workers.cloudflare.com/ is). I think that's all great, by the way, I'm sure it's solving problems that I never even really dealt with at scales that are changing the world.
