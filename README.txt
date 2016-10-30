Copyright (c) 2016 Alex Kramer <kramer.alex.kramer@gmail.com>
See the LICENSE.txt file at the top-level directory of this distribution.

Description
===========
Configurable SCons build tool for Fortran projects. SCons does support Fortran,
but documentation is currently somewhat lacking and some SCons behavior is
slightly unexpected. This attempts to provide a general way to automatically
build more complicated Fortran projects, as well as providing an example of how
to use SCons and Fortran.

Features
========
* Manual specification of source / build directories
* Handling of external libraries in local and system-wide locations
* Toggle between production and debug build flags
* JSON configuration file

Work in Progress Features
=========================
* Automatic install / loading via `site_scons` directory
* Install targets
* Multiple source directory build (currently assumes all source files are
  nested in on source file directory
* Automatic dependency fetching (via Git) and building
* Merging of default and custom JSON configuration files

Basic Usage
===========

* Place (or have symlinks to) the `SConstruct` file in the top-level of this
distribution in the root directory of a project.

* Create / edit a JSON configuration file (specification given below)
describing the build (optional, see below).

* Build project using:

```
scons --config-json /path/to/config/JSON
```

The `--config-json` flag can be omitted; in this case, we assume that there
is a default configuration file named "config_json.default" located in the same
directory as the SConstruct file.

Required Dependencies
=====================

* GNU Fortran (most recently tested on GNU Fortran 6.2)
* SCons (most recently tested on SCons 2.5)

JSON Configuration File Specification
=====================================

Top-level hash map structure
----------------------------

debug :: toggle between debug (true) and production (false) compiler flags
src_path :: path to source file directory
build_path :: path to build directory; must be different than `src_path`
flags :: hash table containing compiler flag specification (see below)
libs :: array containing external library specification (see below)
lib_path_root :: root path for external libraries (see below)

Compiler flag specification
---------------------------

The `flags` JSON field is a hash map with the following structure of logical
groupings (categories) of compiler flags:

```
{
  "flag_name_1": {
    "prod": [boolean],
    "debug": [boolean],
    "list": ["-fflag_1", ..., "-fflag_n"]
  },
  ...
  "flag_name_n": {
    "prod": [boolean],
    "debug": [boolean],
    "list": ["-fflag_1", ..., "-fflag_n"]
  }
}
```

Descriptions of each field are below:

  `flag_name` :: denotes a logical flag grouping
  `prod` :: when true, flags in `flag_name` grouping are used in production
      builds
  `debug` :: when true, flags in `flag_name` grouping are used in debug builds
  `list` :: array of flag strings in logical grouping `flag_name`

External library specification
------------------------------

The `libs` JSON field is an array with the following structure:

```
[
  {
    "name": "lib_1_name",
    "path": "path/to/lib_1",
    "use_root": [boolean]
  }
  ...
  {
    "name": "lib_n_name",
    "path": "path/to/lib_n",
    "use_root": [boolean]
  }
]

Descriptions of each field are below:

  `name` :: library name to use in compiler call
  `path` :: path to library (see `use_root` below)
  `use_root` :: when true, `path` is relative to the top-level `lib_path_root`
    field, when false, `path` is treated as an absolute path
