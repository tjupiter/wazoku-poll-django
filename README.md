# Demo Polls Application

## Description
This repo is intended for testing a simple polls application which captures answers to poll questions from users. Polls are linked to client and each client can have their own user profile form. The application uses Django's in-built Authentication system (https://docs.djangoproject.com/en/3.2/topics/auth/).

## Getting started

Copying the repository

Due to the public nature of forks we suggest you duplicate the repo rather then forking it.
You will need to create your own repo e.g. `[your_github_username]/singlesignon` and then clone
this repo `eshwaric/singlesignon` and push the code into your new one. You can follow the steps for doing this here: https://help.github.com/articles/duplicating-a-repository/

Before proceeding be aware that this exercise assumes you are using a linux machine with [pip](https://pip.pypa.io/en/stable) and [venv](https://docs.python.org/3/library/venv.html) installed.

To initialize the repository in your base directory execute ./initialize_repo.sh

This script will install Django 3.2 and other libraries required for the application. It also loads a fixture to prepopulate the database with some test data for the application

To start the webserver locally, in your base directory execute ./run-server.sh

To run tests locally, in your base directory execute ./run-tests.sh

Access the application at http://localhost:8000
Login credentials have been shared with you via email


## Models description
The application uses 6 models
1. django.contrib.auth.models.User - stores user details and is an inbuilt Django model provided by the authentication system
2. polls.Site - stores details about a tenant
3. polls.Profile - stores additional details for a user. Users can have extra fields and these fields vary for every site based on the profile form. This model refers the _ProfilForm_ and _Site_ model
4. polls.ProfileForm - Each site can have their own profile form and this model stores the fields for the profile forms. This model has a reference to a _Site_.
5. polls.Poll - stores the question for the poll.
6. polls.Answer - stores answers to the poll and the user who provided the answer. This model has a reference to the _Poll_ and _User_ model.

## My solution

To solve the issue:
- I installed the Django Debug Toolbar 
- after looking into the SQL queries it become obvious 
that there were duplicated queries to the server
- QuerySets are lazy and because we have a nested QuerySet 
when we iterate over the answers we access a couple of attributes 
that leads to several queries of the same data
- this is called the N+1 Queries Problem 
- the repetition of queries like `SELECT ... WHERE id = %s` 
is a key signature of the problem. We can spot it in this project at 
`SELECT ••• FROM "auth_user" WHERE "auth_user"."id" = %s` 
- Django provides two QuerySet methods that can turn the N queries 
back into one query, solving the performance issue
These methods are `select_related()` and `prefetch_related()`
- my solution was to introduce a QuerySet for Answers using 
the `prefetch_related` method, then adding `select_related` method as well 
to make one less query 
- I create empty dictionary (`answers_to_poll`) at the
beginning of the iteration of loops
- I used an if statement to make sure we won't end up with diplicates

## Applicant info
### Name: Istvan Pitju Balogh
### Portfolio: https://portfolio.pitju.hu/
### Github: https://github.com/tjupiter

