TYPO3 Extension imageopt
========================

.. image:: https://styleci.io/repos/80069905/shield?branch=master
   :target: https://styleci.io/repos/80069905

.. image:: https://scrutinizer-ci.com/g/sourcebroker/imageopt/badges/quality-score.png?b=master
   :target: https://scrutinizer-ci.com/g/sourcebroker/imageopt/?branch=master

.. image:: https://travis-ci.org/sourcebroker/imageopt.svg?branch=master
   :target: https://travis-ci.org/sourcebroker/imageopt

.. image:: https://poser.pugx.org/sourcebroker/imageopt/license
   :target: https://packagist.org/packages/sourcebroker/imageopt

|
|

.. contents:: :local:

What does it do?
----------------

This extension optimize images resized by TYPO3 so they will take less space,
page will be downloaded faster and Google PageSpeed Insights score will get higher.

Features
--------

- If you enable more than one image optimizer then all of them will be executed
  and the best optimized image is choosen.

- Own providers can be registered with TSconfig.

- Providers can be mixed to create new providers (chained executors).

- Its safe as the original images, for example in folder fileadmin/, uploads/
  are not optmized. Only already resized images are optmized, so for FAL
  that would be files form \_processed\_/ folder and for uploads/ i will be
  typo3temp/pics (TYPO3 7.6 and below) or typo3temp/assets/images (TYPO3 8.7 and higger).

- Adds few xclasses that make TYPO3 to not use original images in frontend HTML. In other words
  you will not find any image in HTML that links directly to /fileadmin/ or /uploads/.
  This way original images do not have to be optimized.

- Support for native linux commands like:

  - for png:

    - optipng
    - pngcrush
    - pngquant

  - for gif:

    - gifsicle

  - for jpeg:

    - jpegoptim
    - jpegrescan
    - jpegtran


Installation
------------

Install the extension using composer:
::

  composer require sourcebroker/imageopt

Configure "_cli_lowlevel" and "_cli_scheduler" users to have access to all filemounts.


Usage
-----

1) Make a direct cli command run to optimize all existing images at once for first time.

   a) For FAL processed images:
      ::

        php ./typo3/cli_dispatch.phpsh extbase imageopt:optimizefalprocessedimages

      or if you use typo3_console:
      ::

        typo3cms imageopt:optimizefalprocessedimages

   b) For folder processed images.
      ::

        php ./typo3/cli_dispatch.phpsh extbase imageopt:optimizefolderimages

      or if you use typo3_console:
      ::

        typo3cms imageopt:optimizefolderimages

      Command "imageopt:optimizefolderimages" will optimize images in following folders:

      - typo3temp/pics/
      - typo3temp/GB/
      - typo3temp/assets/images/

2) For all images which will be processed in future set up scheduler job.


Configuration
-------------

Check https://github.com/sourcebroker/imageopt/blob/master/Configuration/TsConfig/Page/tx_imageopt.tsconfig for
avaliable configuration options.


Technical notes
---------------

* For FAL only files that are in "sys_file_processedfile" are optimized. Table "sys_file_processedfile"
  has  been extended with field "tx_imageopt_executed_successfully". If file has been optimised then the field
  "tx_imageopt_executed_successfully" is set to 1.

  You can reset the "tx_imageopt_executed_successfully" flag with command:
  ::

    php ./typo3/cli_dispatch.phpsh extbase imageopt:resetoptimizationflagforfal

  This can be handy for testing purposes.

* If you optimize files from folders then if file has been optimized it gets "executed" persmission bit. So for most
  cases its 644 on the beginning and 744 after optimization. The "execution" bit is the way script knows which files
  has been optimized and which one still needs.

  You can reset the "executed" bit for folders decalred in "tx_imageopt.directories" with command:
  ::

    php ./typo3/cli_dispatch.phpsh extbase imageopt:resetoptimizationflagforfolders

  This can be handy for testing purposes.

* There is table "tx_imageopt_domain_model_optimizationresult" with relation to two more tables
  "tx_imageopt_domain_model_providerresult" and "tx_imageopt_domain_model_executorresult".
  They hold statistics from  images optimizations. You can check there what command exactly was
  used to optimize image, what was the result, error,  how many bytes image has before and after
  for each executor and for each provider.


Changelog
---------

See https://github.com/sourcebroker/imageopt/blob/master/CHANGELOG.rst
