# Integration Tests for the [Mozilla Addons Website][amo].
## How to run the tests locally
### Clone the repository

If you have cloned this project already then you can skip this, otherwise you'll
need to clone this repo using Git. If you do not know how to clone a GitHub
repository, check out this [help page][git-clone] from GitHub.

If you think you would like to contribute to the tests by writing or maintaining
them in the future, it would be a good idea to create a fork of this repository
first, and then clone that. GitHub also has great instructions for
[forking a repository][git-fork].

### Server Install

Follow the instructions found [here][addons-server-docs].

### Server setup for tests

The instructions to setup addons-server for selenium integration tests are found [here][addons-server-selenium-testing].

### Run the tests

The selenium tests can be run in a separate docker-compose file, specifically ``tests/ui/docker-compose.selenium.yml``. This image contains Firefox Nightly. [tox][Tox]
is our test environment manager and [pytest][pytest] is the test runner.

To run the tests, execute the commands below:
```sh
# Make sure all required containers are running.
docker-compose -f docker-compose.yml -f tests/ui/docker-compose.selenium.yml up -d
docker-compose -f docker-compose.yml -f tests/ui/docker-compose.selenium.yml exec selenium-firefox tox -e ui-tests
```

### Adding a test

The tests are written in Python using a POM, or Page Object Model. The plugin we use for this is called [pypom][pypom]. Please read the documentation there for good examples
on how to use the Page Object Model when writing tests.

The pytest plugin that we use for running tests has a number of advanced command
line options available too. The full documentation for the plugin can be found [here][pytest-selenium].

## Additional Information

The tests run against the newest version of the [AMO][amo] frontend using a docker image provided by [addons-frontend][addons-frontend]. You can view the frontend after the build has been completed at ```olympia.test``` which is the default these days.

### Watching a test run

The tests are run on a live version of Firefox, but they are run headless. To access the container where the tests are run to view them follow these steps. NOTE: You must have the appropriate containers set up. Please follow the instructions in the [docs][ReadTheDocs] first:

1. Make sure all of the containers are running:
```sh
docker-compose ps
```
If not, start them detached:
```sh
docker-compose -f docker-compose.yml -f tests/ui/docker-compose.selenium.yml up -d
```

2. Copy the port that is forwarded for the ```selenium-firefox``` image:
```sh
0.0.0.0:32771->5900/tcp
```
Note: Your port may not match what is seen here.

You will want to copy what ever IP address and port is before the ```->5900/tcp```.

3. Open your favorite VNC viewer and type in, or paste that address.
4. The password is ```secret```.
5. The viewer should open a window with a Ubuntu logo. If that happens you are connected to the ```selenium-firefox``` image and if you start the test, you should see a Firefox window open and the tests running.

If you have [geckodriver][geckodriver] installed, and Firefox can be launched from your terminal, you can run this command:

```sh
tox -r -e ui-tests
```
This will open a Firefox window with the tests running.

### Firefox setup

The preferences used to setup Firefox are here:
```sh
    firefox_options.set_preference(
        'extensions.install.requireBuiltInCerts', False)
    firefox_options.set_preference('xpinstall.signatures.required', False)
    firefox_options.set_preference('extensions.webapi.testing', True)
    firefox_options.set_preference('ui.popup.disable_autohide', True)
    firefox_options.add_argument('-foreground')
    firefox_options.log.level = 'trace'
```
These shouldn't need to be touched as they allow for unsigned addon installation as well as
disabling the autohide function and setting the Firefox browser to run headless.

If you do need to edit these settings, as mentioned above please visit the file ```conftest.py``` within this directory.

### Mobile and Desktop testing

If you would like to add or edit tests please consider that these are run on both a mobile resolution and a desktop resolution. The mobile resolution is ```738x414 (iPhone 7+)```, the desktop resolution is: ```1920x1080```. Your tests should be able to work on both.


### Debugging a failure

Whether a test passes or fails will result in a HTML report being created. This report will have detailed information of the test run and if a test does fail, it will provide geckodriver logs, terminal logs, as well as a screenshot of the browser when the test failed. We use a pytest plugin called [pytest-html][pytest-html] to create this report. The report can be found within the root directory of the project and is named ```ui-test.html```. It should be viewed within a browser.

[amo]: https://addons.mozilla.org
[addons-frontend]: https://github.com/mozilla/addons-frontend/
[addons-server-docs]: https://addons-server.readthedocs.io/en/latest/topics/install/docker.html
[addons-server-selenium-testing]: https://addons-server.readthedocs.io/en/latest/topics/development/testing.html#selenium-integration-tests
[flake8]: http://flake8.pycqa.org/en/latest/
[git-clone]: https://help.github.com/articles/cloning-a-repository/
[git-fork]: https://help.github.com/articles/fork-a-repo/
[geckodriver]: https://github.com/mozilla/geckodriver/releases/tag/v0.19.1
[pypom]: http://pypom.readthedocs.io/en/latest/
[pytest]: https://docs.pytest.org/en/latest/
[pytest-html]: https://github.com/pytest-dev/pytest-html
[pytest-selenium]: http://pytest-selenium.readthedocs.org/
[ReadTheDocs]: https://addons-server.readthedocs.io/en/latest/topics/development/testing.html#selenium-integration-tests
[Selenium]: http://selenium-python.readthedocs.io/index.html
[selenium-api]: http://selenium-python.readthedocs.io/locating-elements.html
[Tox]: http://tox.readthedocs.io/
plugin we use for this is called [pypom][pypom]. Please read the documentation there for good examples
on how to use the Page Object Model when writing tests.

The pytest plugin that we use for running tests has a number of advanced command
line options available too. The full documentation for the plugin can be found [here][pytest-selenium].

## Additional Information

The tests run against the newest version of the [AMO][amo] frontend using a docker image provided by [addons-frontend][addons-frontend]. You can view the frontend after the build has been completed at ```olympia.test``` which is the default these days.

### Watching a test run

The tests are run on a live version of Firefox, but they are run headless. To access the container where the tests are run to view them follow these steps:

IMPORTANT: Please comment out this line within the ```tests/ui/conftest.py``` file if you would like to view or debug the tests.
```sh
firefox_options.add_argument('-headless')
```

1. Make sure all of the containers are running:
```sh
docker-compose ps
```
If not start them detached:
```sh
docker-compose -f docker-compose.yml -f tests/ui/docker-compose.selenium.yml up -d
```

2. Copy the port that is forwarded for the ```selenium-firefox``` image:
```sh
0.0.0.0:32771->5900/tcp
```
Note: Your port may not match what is seen here.

You will want to copy what ever IP address and port is before the ```->5900/tcp```.

3. Open your favorite VNC viewer and type in, or paste that address.
4. The password is ```secret```.
5. The viewer should open a window with a Ubuntu logo. If that happens you are connected to the ```selenium-firefox``` image and if you start the test, you should see a Firefox window open and the tests running.

### Firefox setup

The preferences used to setup Firefox are here:
```sh
    firefox_options.set_preference(
        'extensions.install.requireBuiltInCerts', False)
    firefox_options.set_preference('xpinstall.signatures.required', False)
    firefox_options.set_preference('extensions.webapi.testing', True)
    firefox_options.set_preference('ui.popup.disable_autohide', True)
    firefox_options.add_argument('-foreground')
    firefox_options.add_argument('-headless')
    firefox_options.log.level = 'trace'
```
These shouldn't need to be touched as they allow for unsigned addon installation as well as
disabling the autohide function and setting the Firefox browser to run headless.

If you do need to edit these settings, as mentioned above please visit the file ```conftest.py``` within this directory.

### Mobile and Desktop testing

If you would like to add or edit tests please consider that these are run on both a mobile resolution and a desktop resolution. The mobile resolution is ```738x414 (iPhone 7+)```, the desktop resolution is: ```1920x1080```. Your tests should be able to work on both.


### Debugging a failure

Whether a test passes or fails will result in a HTML report being created. This report will have detailed information of the test run and if a test does fail, it will provide geckodriver logs, terminal logs, as well as a screenshot of the browser when the test failed. We use a pytest plugin called [pytest-html][pytest-html] to create this report. The report can be found within the root directory of the project and is named ```ui-test.html```. It should be viewed within a browser.

[amo]: https://addons.mozilla.org
[addons-frontend]: https://github.com/mozilla/addons-frontend/
[addons-server-docs]: https://addons-server.readthedocs.io/en/latest/topics/install/docker.html
[flake8]: http://flake8.pycqa.org/en/latest/
[git-clone]: https://help.github.com/articles/cloning-a-repository/
[git-fork]: https://help.github.com/articles/fork-a-repo/
[geckodriver]: https://github.com/mozilla/geckodriver/releases/tag/v0.19.1
[pypom]: http://pypom.readthedocs.io/en/latest/
[pytest]: https://docs.pytest.org/en/latest/
[pytest-html]: https://github.com/pytest-dev/pytest-html
[pytest-selenium]: http://pytest-selenium.readthedocs.org/
[Selenium]: http://selenium-python.readthedocs.io/index.html
[selenium-api]: http://selenium-python.readthedocs.io/locating-elements.html
[Tox]: http://tox.readthedocs.io/
