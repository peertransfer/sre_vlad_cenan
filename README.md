# World temperatures

[Sinatra](http://www.sinatrarb.com/) is a free and open source software web application library and domain-specific language written in Ruby. Setup instructions are included in INSTRUCTIONS.md.

Your goal is to transform the installation instructions into an agnostic configuration management tool that will install and initialize the application, additionally you need to do changes in the application to change the behaviour.

The application gather weather temperature from any location and display the temperature in the local system units ( f.e. Boston in F and Valencia in C ) in the index page, so you can fork the sinatra-skeleton repo to make these changes and update repository from INSTRUCTIONS.md accordingly.

(Hint: To gather temperature data you can use the following [example](https://gist.github.com/mjamieson/5274790#file-forecast-rb) with token `99f08919415972d78b757e13a6fc1345` and you can use `http://maps.googleapis.com/maps/api/geocode/json?address=46021+Valencia,Spain&sensor=false` to get the latitude and longitude.)

The updated application should look like this:

![Valencia](http://i.imgur.com/NLQobcl.png)

## Objectives

* Run application in Debian Jessie
* Deployed in another Linux flavour
* Testing
* Infrastructure agnostic ( VirtualBox, Amazon, VMware, DigitalOcean )

## Pre-requisites

Before beginning you will need:

* A virtual machine or server running Debian Jessie
* A text editor
* A version control system

## Completion Criteria

You'll know this exercise is complete when:

* You can provision your infrastructure multiple times without failures.
* Your source code repository shows the history of your work.
* Multi-OS support
* Always is nice to see some Dockers in action
* Use at least one custom resource (chef) or equivalent in your favorite config management system

Note: We are looking for code quality and patterns if you don't have enougth time to finish don't worry you can justify why later in the exercise review.

## Extraball
We need a CLI application written in golang that transforms emoji tags like `:beer:` into emoji 🍺.
Interface should be like this one:
```
$ ./emo_getter
A longer description that spans multiple lines

Usage:
  emo_getter [command]

Available Commands:
  emoji       A brief description of your command
  help        Help about any command

Flags:
      --config string   config file (default is $HOME/.emo_getter.yaml)
  -h, --help            help for emo_getter
  -t, --toggle          Help message for toggle

Use "emo_getter [command] --help" for more information about a command.
```
And output should be like this
```
$ ./emo_getter emoji :beer:
🍺
$ ./emo_getter emoji :octopus:
🐙
```
Goals:
* We need at least 5 different emojis
* We need minimum sice Docker as resultant artifact
