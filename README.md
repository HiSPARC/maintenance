HiPSARC Maintenance Documentation
=================================

This repository contains the source files and gh-pages for the HiSPARC maintenance documentation.

The build version of these pages can be viewed here:
http://hisparc.github.com/maintenance/

Making a build from the source files requires Sphinx.

To make a html and pdflatex build for gh-pages of the current master branch; use the 'make gh-pages' command while in the main directory of the master branch. This will checkout the gh-pages branch, make the builds, does some cleanup, then commits the new version and goes back to the master branch.

The automatic gh-pages build command might not work on Windows systems at the moment.
