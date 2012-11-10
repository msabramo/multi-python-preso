<!SLIDE smaller intro>

# Making Projects Work With Multiple Versions of Python

## Marc Abramowitz
## http://marc-abramowitz.com/
## @msabramo
## marc@marc-abramowitz.com
## github.com/msabramo/django-testing-slides



<!SLIDE>

# Outline

* Targeting multiple pythons: the problem
* Testing against multiple pythons
	* Tox
	* Travis CI
* Multi-Python Compatibility Approaches
	* Translation tools - distribute's `use_2to3`
	* Writing multi-python code - `six`



<!SLIDE>

# Problem: Multiple Pythons

* 2.4, 2.5, 2.6, 2.7
* 3.0, 3.1, 3.2, 3.3
* PyPy
* Jython



<!SLIDE>

# Differences in Python 2.x versions

* `with` statement (2.5)
* `except...as` syntax (2.6)
* `unittest` (2.7)



<!SLIDE>

# Differences between Python 2.x and Python 3.x

.notes even more dramatic differences

* `print` statement vs. `print` function
* Unicode handling
	* `str` and `unicode` vs. `bytes` and `str`
	* `u` and `b` syntax for string literals
* moved classes and renamed modules
	* `httplib` => `http.client`
	* `StringIO.StringIO` => `io.StringIO`



<!SLIDE section>

# Testing against multiple Pythons



<!SLIDE>

# Goal

* Be able to quickly know whether code works in various versions of Python



!SLIDE Tox
# Tox

!SLIDE Tox
# Tox

* By Holger Kregel ([`py.test`](http://pytest.org/), [`execnet`](http://codespeak.net/execnet/))
* [http://tox.testrun.org/](http://tox.testrun.org/)
* `pip install tox`

!SLIDE Tox (continued)
# Tox

* A virtualenv manager
* Tests your stuff in multiple Pythons using `virtualenv`
* Everything happens **locally**

!SLIDE Using Tox
# Using Tox

* Prerequisites
	* Install pythons
	* Create `setup.py` for project
* Create a `tox.ini` file
* Run `tox`

.notes Pythons can be installed manually or you can use something like pythonbrew, but it's your responsibility.
This is in constrast to Travis CI, which we will see in a bit...

!SLIDE Tox.ini
# Tox.ini

* A `.ini` file
* Main sections are:
	* `tox`
	* `testenv`

!SLIDE tox.ini
# Tox.ini

    [tox]
    envlist = py26, py27, py32, py33, pypy
    
    [testenv]
    commands = nosetests

!SLIDE Other Tox features
# Other Tox features
 
 * `tox -e py32,py33`
 * `deplist` - specify packages needed
 * environment-specific sections
	 * e.g.: `[testenv:py26]`
* variable substitutions

.notes `deplist` overlaps with putting dependencies in `setup.py`, but useful for putting test-only dependencies

!SLIDE Tox summary
# Tox summary

* Prerequisites
	* Install Pythons
	* Create `setup.py`
* Create `tox.ini`
* Hack and test **locally**

!SLIDE Creating tox.ini
# Creating `tox.ini`

* Copy one from another project
* [https://github.com/msabramo/python-panci](https://github.com/msabramo/python-panci)
    * `panci --to=tox .travis.yml`
	* `panci-tox-quickstart`
		* Trying to add to tox itself ([PR 20](https://bitbucket.org/hpk42/tox/pull-request/20/add-a-tox-quickstart-command/diff))

!SLIDE Travis CI
# Travis CI

!SLIDE Travis CI
# Travis CI

* [http://about.travis-ci.org/](http://about.travis-ci.org/)
* CI in the cloud, via virtual machines
* You don't install Pythons
* Triggered by GitHub service hooks

!SLIDE Travis-CI is free and open-source
# Free and open-source

* Though there is a Pro version in beta for private repos

!SLIDE Travis-CI is language-agnostic
# Language-agnostic

* C, C++, Clojure, Erlang, Go, Groovy, Haskell, Java, JavaScript (with Node.js), Perl, PHP, Python, Ruby, Scala

!SLIDE Travis CI environment goodies
# Environment goodies

* gcc, clang, make, autotools, cmake, scons
* MySQL, PostgreSQL, MongoDB, Redis, RabbitMQ, ZeroMQ, ElasticSearch, etc.
* PhantomJS, xvfb, Firefox
* Allows doing a lot of forms of testing

.notes Environments include several versions of Python and a host of build tools, data stores, messaging software, etc. which can be useful for testing. Details at [http://about.travis-ci.org/docs/user/ci-environment/](http://about.travis-ci.org/docs/user/ci-environment/), [http://about.travis-ci.org/docs/user/database-setup/](http://about.travis-ci.org/docs/user/database-setup/), and [http://about.travis-ci.org/docs/user/gui-and-headless-browsers/](http://about.travis-ci.org/docs/user/gui-and-headless-browsers/)

!SLIDE Setting up Travis CI
# Setting up Travis CI

* Create `.travis.yml`
* Set up GitHub service hook
* Commit, push, test

!SLIDE Sample `.travis.yml`
# Sample `.travis.yml`

	language: python

	python:
	  - 2.5
	  - 2.6
	  - 2.7
	  - 3.2
	  - pypy

	install: python setup.py install

	script: python setup.py test

!SLIDE Creating `.travis.yml`
# Creating `.travis.yml`

* Copy one from another project
* [https://github.com/msabramo/python-panci](https://github.com/msabramo/python-panci)
    * `panci --to=travis tox.ini`
* Use [`travis-lint` web app or CLI tool](http://about.travis-ci.org/docs/user/travis-lint/) to check your syntax

!SLIDE GitHub service hook
# GitHub service hook

* Sign in with GitHub

![Sign in with GitHub](http://f.cl.ly/items/0A31312A0j1N3D1G351B/Screen%20shot%202012-11-05%20at%2011.39.53%20AM.png)

!SLIDE GitHub service hook screenshot

![Enabling Travis for repos](http://f.cl.ly/items/3E141W2B0y44130g023d/Screen%20shot%202012-11-09%20at%202.21.11%20PM.png)

!SLIDE Push to test
# Push to test

* `git push`
* Watch the Web UI
* Or wait for notification via email or IRC

!SLIDE Tox vs. Travis CI
# Tox vs. Travis CI

* Which one? Or both?

!SLIDE Tox
# Tox

* locally run
* requires more setup
* faster feedback loop
* can test before committing

!SLIDE Travis CI
# Travis CI

* cloud-based
* less setup
* uses virtual machines = more isolated
* slower feedback loop - have to push and sometimes wait in queue

!SLIDE ...Or use both
# ...Or use both

* You can use both to get advantages of both
* Disadvantage: more files to maintain
	* Might be possible to address by generating one from another -- e.g.: [panci]((https://github.com/msabramo/python-panci))

.notes annoying to maintain dependencies in setup.py, requirements.txt, tox.ini, and .travis.yml; mention panci & travis2tox & tox2travis...?

!SLIDE Multi-Python Compatibility
# Multi-Python Compatibility

!SLIDE Multi-Python Compatibility Approaches
# Approaches

* Use tools for testing - Tox and Travis CI
* Leverage translation tools like `2to3`
* Write code that works with multiple versions

!SLIDE Strategies - tradeoffs
# Strategies - tradeoffs

* Translation tools
	* Sort of a crutch.
	* Prevent you from using new features - e.g.: Python 3's unicode handling.
* Writing multi-python code 
	* Can sometimes get a bit ugly.
	* Libraries like `six` can help.

!SLIDE Translation: 2to3
# Translation: 2to3

* CLI tool that comes with recent Pythons
* Translates from Python 2 to 3
* But then it doesn't work in Python 2
* Python 3 also comes with a 3to2
* These tools encourage forking and having separate Python 2 and 3 codebases. Yuck. 

!SLIDE Translation: distribute's `use_2to3` feature
# Translation: distribute's `use_2to3` feature

* Put `use_2to3=True` in `setup.py`
* Translates **on the fly** when user does `python setup.py install`.
* Developer writes only Python 2 code.

!SLIDE `use_2to3` caveat
# `use_2to3` caveat

* If Tox or Travis runs tests in the source directory, it will likely pick up the original, untranslated module(s) => errors with Python 3
* One way to solve:

	`nosetests -w /tmp mypackage.tests`

!SLIDE Multi python c0de: Problems
# Multi-python code: Problems

* Handling `str` and `unicode` vs. `bytes` and `str`
* Literal notation like `u''` and `b''`
* Moved classes and modules. 
	* `StringIO.StringIO` => `io.StringIO`
	* `xrange` => `range`
	* `httplib` => `http.client`

!SLIDE Example multi-python c0de (1)
# Example multi-python code (1)

    try:
        # Python 2
        unicode
    except NameError:
        # Python 3
    	unicode = str

!SLIDE Example multi-python c0de (2)
# Example multi-python code (2)

	try:
		# Python 2
	    from StringIO import StringIO
	except ImportError:
		# Python 3
	    from io import StringIO

	s = StringIO()

!SLIDE Example multi-python c0de (3)
# Example multi-python code (3)

	if PY3:
	    def b(s):
	        return s.encode("latin-1")
	    def u(s):
	        return s
	else:
		def b(s):
		    return s
		def u(s):
		    return unicode(
			    s, "unicode_escape")

!SLIDE This can get a big ugly...
# This can get a big ugly...

!SLIDE Python3Porting.com
# Python3Porting.com

* Lennart Regebro
* Porting to Python 3
* Book (no longer in print) and [web site](http://python3porting.com)
* Tons of tips for dealing with porting situations

.notes Picture of Python3Porting.com?
.notes Migration strategies
Preparing for Python 3
Porting with 2to3
Common migration problems
Improving your code with modern idioms
Supporting Python 2 and 3 without 2to3 conversion
Migrating C extensions
Extending 2to3 with your own fixers
Language differences and workarounds
Reorganizations and renamings

!SLIDE 2 x 3 = 6
# 2 x 3 = 6

!SLIDE The `six` module
# The `six` module

* Python 2 and 3 compatibility library
* by Benjamin Peterson (Python core dev)
* [http://pypi.python.org/pypi/six/](http://pypi.python.org/pypi/six/)
* Has lots of abstractions for things that differ

!SLIDE Using `six`
# Using `six`

	from six import u, b

	print(u('Bj\xf6rk Gu\xf0mundsd\xf3ttir'))

!SLIDE Using `six` (2)
# Using `six`

	from six import StringIO

	s = StringIO()

!SLIDE `six` makes writing multi-python c0de easier
# `six` makes writing multi-python code easier

!SLIDE Test across Pythons
# Test across Pythons

* Whether you write multi-python code (with or without `six`)...
* Or use `use_2to3`
* Test across multiple Pythons with Tox and/or Travis

!SLIDE Conclusion
# Conclusion

* Targeting multiple pythons: the problem
* Testing against multiple pythons
	* Tox
	* Travis CI
* Multi-Python Compatibility Approaches
	* Translation tools - distribute's `use_2to3`
	* Writing multi-python code - `six`

