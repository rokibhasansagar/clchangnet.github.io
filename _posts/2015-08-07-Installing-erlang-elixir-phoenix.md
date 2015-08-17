---
layout: post
title: Erlang, Elixir and Phoenix
---

Setup guide to install erlang, elixir and phoenix on a ubuntu trusty 14.04 linux system. Differs from official installation when installing erlang and elixir. Follow this guide to install the correct version according to system linux version.

## Install erlang

Download [erlang][1]
Select enterprise distribution, ubuntu trusty 32 or 64 bit (depend on your linux installation), download complete, click to install

Test erlang

    $ erl
    Erlang/OTP 17 [RELEASE CANDIDATE 2] [erts-6.0] [source] [smp:2:2] [async-threads:10] [hipe] [kernel-poll:false]
    Eshell V6.0  (abort with ^G)
    1> io:format("Hello World!~n").
    Hello World!
    ok
    2>

[1]: https://www.erlang-solutions.com/downloads/download-erlang-otp

## Install elixir

Download [elixir][2]
Select ubuntu trusty 32 or 64 bit (depend on your linux installation), download complete, click to install

Test elixir

    $ iex
    Erlang/OTP 17 [RELEASE CANDIDATE 2] [erts-6.0] [source] [smp:2:2] [async-threads:10] [hipe] [kernel-poll:false]
    Interactive Elixir (0.13.0-dev) - press Ctrl+C to exit (type h() ENTER for help)
    iex(1)> IO.puts "Hello World!"
    Hello World!
    :ok
    iex(2)>

[2]: https://www.erlang-solutions.com/downloads/download-elixir

## Install phoenix

    $ mix local.hex
    $ mix archive.install https://github.com/phoenixframework/phoenix/releases/download/v0.16.1/phoenix_new-0.16.1.ez


## Install nodejs


    # Note the new setup script name for Node.js v0.12
    curl -sL https://deb.nodesource.com/setup_0.12 | sudo bash -

    # Then install with:
    sudo apt-get install -y nodejs


## Install inotifytools
    sudo apt-get install inotify-tools

## Final - Create app and run

    mix phoenix.new hello_phoenix
    cd hello_phoenix
    mix phoenix.server

Goto http://localhost:4000, if successful, you should see phoenix framework welcome screen.