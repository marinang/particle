.. image:: https://github.com/scikit-hep/particle/raw/master/docs/ParticleLogo300.png
    :alt: particle
    :target: https://github.com/scikit-hep/particle

Particle: PDG particle data and identification codes
====================================================

.. image:: https://badge.fury.io/py/particle.svg
  :alt: PyPI
  :target: https://badge.fury.io/py/particle

.. image:: https://zenodo.org/badge/DOI/10.5281/zenodo.2552429.svg
  :target: https://doi.org/10.5281/zenodo.2552429

.. image:: https://dev.azure.com/scikit-hep/particle/_apis/build/status/scikit-hep.particle?branchName=master
  :alt: Build Status
  :target: https://dev.azure.com/scikit-hep/particle/_build/latest?definitionId=1?branchName=master

.. image:: https://img.shields.io/azure-devops/coverage/scikit-hep/particle/1.svg
  :alt: Coverage
  :target: https://dev.azure.com/scikit-hep/particle/_build/latest?definitionId=1?branchName=master

.. image:: https://img.shields.io/azure-devops/tests/scikit-hep/particle/1.svg
   :alt: Tests
   :target: https://dev.azure.com/scikit-hep/particle/_build/latest?definitionId=1?branchName=master


Particle provides a pythonic interface to the `Particle Data Group <http://pdg.lbl.gov/>`_ (PDG)
particle data tables and particle identification codes.

The PDG defines the standard particle identification (ID) numbering scheme.
The package provides the ``PDGID`` class implementing queries on those PDG IDs.
The queries are also accessible through free standing functions mimicking the
HepPID C++ interface.

The ``Particle`` class wraps the information in the PDG particle data tables and
provides an object-oriented interface and powerful search and look-up utilities.

The current version of the package reflects a pythonic version of the
utility functions defined in HepPID and HepPDT versions 3.04.01,
see http://lcgapp.cern.ch/project/simu/HepPDT/.

Installation
------------

Install ``particle`` like any other Python package:

.. code-block:: bash

    pip install particle

or similar (use ``--user``, ``virtualenv``, etc. if you wish).

Strict dependencies
-------------------

- `Python <http://docs.python-guide.org/en/latest/starting/installation/>`_ (2.7+, 3.5+)
- `importlib_resources backport <http://importlib-resources.readthedocs.io/en/latest/>`_ if using Python < 3.7
- `attrs <http://www.attrs.org/en/stable/>`_ provides classes without boilerplate (similar to DataClasses in Python 3.7)

Getting started: PDGIDs
-----------------------

.. code-block:: python

    >>> from particle.pdgid import PDGID
    >>>
    >>> pid = PDGID(211)
    >>> pid
    <PDGID: 211>
    >>> pid.is_meson
    True
    >>> pid = PDGID(99999999)
    >>> pid
    <PDGID: 99999999 (is_valid==False)>

For convenience, all properties of the ``PDGID`` class are available as standalone functions:

.. code-block:: python

    >>> from particle.pdgid import is_meson
    >>>
    >>> is_meson(211)
    True

PDGID literals provide (``PDGID`` class) aliases for the most common particles, with easily recognisable names.
For example:

.. code-block:: python

    >>> from particle.pdgid import literals as lid
    >>>
    >>> lid.pi_plus
    <PDGID: 211>
    >>>
    >>> from particle.pdgid.literals import Lambda_b_0
    >>>> Lambda_b_0
    <PDGID: 5122>
    >>> Lambda_b_0.has_bottom
    True

You can quickly display PDGID info from the command line with:

.. code-block:: bash

    $ python -m particle pdgid 323
    <PDGID: 323>
    A              None
    J              1.0
    L              0
    S              1
    Z              None
    abspid         323
    charge         1.0
    has_bottom     False
    ...

Getting started: Particles
--------------------------

You can use a variety of methods to get particles. If you know the PDGID number
you can get a particle directly, or you can use a search:

.. code-block:: python

    >>> from particle import Particle
    >>> Particle.from_pdgid(211)
    <Particle: pdgid=211, name='pi+', mass=139.57061 ± 0.00024 MeV>
    >>>
    >>> Particle.from_search_list('pi')[0]
    <Particle: pdgid=111, name='pi0', mass=134.9770 ± 0.0005 MeV>

You can search for the properties using keyword arguments, which include
``pdgname``, ``name``, ``mass``, ``width``, ``charge``, ``anti``, ``rank``,
``I``, ``J``, ``G``, ``P``, ``quarks``, ``status``, ``latex``,
``mass_upper``, ``mass_lower``, ``width_upper``, and ``width_lower``.
You can pass a callable or an exact match for any property.  `particle` can be
set to `True`/`False`, as well, to limit the search to particles or
antiparticles.  You can also build the search yourself with the first positional
argument, which accepts a callable that is given the particle object itself. If
the first positional argument is a string, that will match against the
particle's ``name``.  The alternative ``.from_search()`` *requires only one*
match returned by the search, and will throw an error if more or less than one
match is found.

Once you have a particle, any of the properties can be accessed, along with several methods.
Though they are not real properties, you can access ``bar``, ``radius``, and ``spin_type``.
You can also ``.invert()`` a particle.

There are lots of printing choices for particles:
``describe()``, ``programmatic_name``, ``html_name``, HTML printing outs in notebooks,
and of course ``repr`` and ``str`` support.

You can get the ``.pdgid`` from a particle, as well.
Sorting particles will put lowest abs(PDGID) first.


Particle literals provide (``Particle`` class) aliases for the most common particles,
with easily recognisable names. For example:

.. code-block:: python

    >>> from particle.particle import literals as lp
    >>> lp.pi_plus
    <Particle: pdgid=211, name='pi+', mass=139.57061 ± 0.00024 MeV>
    >>>
    >>> from particle.particle.literals import Lambda_b_0
    >>>> Lambda_b_0
    <Particle: pdgid=5122, name='Lambda(b)0', mass=5619.60 ± 0.17 MeV>
    >>> Lambda_b_0.J
    0.5

You can quickly search for particles from the command line with:

.. code-block:: bash

    $ python -m particle search 'K*0'
    <Particle: pdgid=313, name='K*(892)0', mass=895.55 ± 0.20 MeV>
    <Particle: pdgid=30313, name='K*(1680)0', mass=1718 ± 18 MeV>
    <Particle: pdgid=100313, name='K*(1410)0', mass=1421 ± 9 MeV>

If you only select one particle, either by a search or by giving the PDGID number, you can see more information about
the particle:

.. code-block:: bash

    $ python -m particle search 311
    PDG name: K          ID: 311          Name: K0             Latex: $K^{0}$
    Mass  = 497.611 ± 0.013 MeV
    Width = -1.0 MeV
    I (isospin)       = 1/2    G (parity)        = 0      Q (charge)       = 0
    J (total angular) = 0.0    C (charge parity) = 0      P (space parity) = ?
        Quarks: dS
        Antiparticle status: Full (antiparticle name: K~0)
