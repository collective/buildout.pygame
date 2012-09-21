Pygame and friends: a Buildout love story
=========================================

Once upon a time, there was a software build solution called Buildout
and a Python library called `Pygame`_.  Originally, they didn't play well
together, but eventually they got on well enough.

This is their story...

Rationale
---------

`Pygame`_ is an interesting beast.  It uses a variety of platform-specific
configuration and egg setup generation techniques (that I really don't
understand) and does a lot of things by hand.  This makes it somewhat difficult
to play and deploy with.

Pygame, whilst possible to install via any regular method (such as
``zc.recipe.egg``, or ``pip install``), some of its extra modules have a number
of C library and other external dependencies.  It's easy enough if you're on a
Linux machine (such as Ubuntu) that has these libraries in the standard
repositories, but if you're on one that doesn't (such as RHEL or CentOS), or if
you don't want or can't depend on system libraries, then this is why this
buildout exists.

This buildout currently supports the ``Font``, ``Image`` and ``Mixer`` modules
of Pygame. The extra Pygame dependencies this buildout currently supports are
detailed as follows (excerpt from install process)::

    SDL     : found 1.2.15
    FONT    : found
    IMAGE   : found
    MIXER   : found
    SMPEG   : not found
    PNG     : found
    JPEG    : found
    SCRAP   : not found
    PORTMIDI: not found
    PORTTIME: not found
    AVFORMAT: not found
    SWSCALE : not found
    FREETYPE: found 2.4.10

Feel free to jump in and add support. 

Usage
-----

Extend the ``pygame.cfg`` file in your buildout's ``extends`` and include
``${pygame:parts}`` in your ``parts`` option of ``[buildout]``, like so::

    [buildout]
    extends = https://github.com/collective/buildout.pygame/raw/master/pygame.cfg
    parts = 
        ${pygame:parts}
        ...

This buildout provides an `autoenv`_ ``.env`` file into your buildout directory
in case you need to have your library path environment variables adjusted to
suit these newly compiled libraries, and/or have your PATH itself updated to
know about the compiled executables.  If you don't have `autoenv`_ or don't
want to use this, then you can always ``source`` the file like normal. In
testing, however, it was only necessary to have the libraries present to have
Pygame compile correctly - once compiled, it seemed to not care about not
knowing where the C libraries were. YMMV.

Requirements
------------

There's several prerequisites to using this buildout since we're cloning from
source code, compiling C and other libraries, and more.  This list probably
isn't exhaustive yet so help add to this if something's missing when you
run the buildout on your system:

* gcc
* make
* git
* hg

Also, you need to have a source-code clone of pygame in your buildout's
``src`` directory. You can achieve this by running::

    hg clone pygame https://bitbucket.org/davidjb/pygame

or add the following to your ``mr.developer`` configuration for Buildout::
    
    [sources]
    pygame = hg https://bitbucket.org/davidjb/pygame egg=false update=false

This special version of pygame features a local directory configuration fix
that ensures local lib directories take precedence over system directories.
There's currently a pull request in over on Bitbucket to have this patch 
accepted.

.. _Pygame: http://pygame.org/
.. _autoenv: https://github.com/kennethreitz/autoenv
