+++
title = "Mass Editing Similar Files"
description = ""
date = 2017-04-03

[taxonomies]
tags = ["workflows"]
+++

There are occasionally situations where you may need to edit multiple files
that are all similar but not exactly the same. In these occasions, the method
presented here can be useful for saving a lot of your valuable time.

<!-- more -->

This method leverages the power of git, diff, and patch in order to apply a
single change across multiple files which are similar. [You are using version
control, right?][vcs-for-papers]. These instructions you are either using a
Unix like system (macOS or Linux), or Cygwin in Windows, the basic idea is
simple:

1. `git diff <edited file> > changes.patch`
2. `find . -name <file name> -exec patch -p1 {} changes.patch \;`

Where `<edited file>` is the full path to the file with your changes. You can
also make more complex patches if you want, just read about
[git-diff][git-diff].

Yes, the arguments to `find` are single dashes with full word arguments.

## Example Usage

It's often easier to see how to apply something when you get to see an example.
Imagine a file structure such as:

```
~/Desktop/example_project
▶ tree .
.
├── common
│   └── config.h
├── project_1
│   ├── config.h
│   └── project_config.h
├── project_10
│   ├── config.h
│   └── project_config.h
├── project_2
│   ├── config.h
│   └── project_config.h
├── project_3
│   ├── config.h
│   └── project_config.h
├── project_4
│   ├── config.h
│   └── project_config.h
├── project_5
│   ├── config.h
│   └── project_config.h
├── project_6
│   ├── config.h
│   └── project_config.h
├── project_7
│   ├── config.h
│   └── project_config.h
├── project_8
│   ├── config.h
│   └── project_config.h
└── project_9
    ├── config.h
    └── project_config.h

11 directories, 21 files
```

In this example you want to add a single line to project_config.h, but don't
want to have to manually edit 10 different files. To get around this, you would
edit any one of the project_config.h files with your changes:

```
▶ echo "#define A_NEW_CONFIG_OPTION" >> project_1/project_config.h
```

Finally, run the diff-patch process:

```
git diff project_1/project_config.h > changes.patch
find . -name project_config.h -exec patch -p1 {} changes.patch \;
```

## Possible Caveats

This section lists some of the issues I've run into with this workflow. These
issues mostly only apply to Windows.

* Cygwin doesn't come with patch installed by default, install it.
* Windows also has a find utility, if `/usr/bin` isn’t fairly early in your PATH
  then Cygwin will use that instead. You’ll get an error message like “FIND:
  Parameter format not correct”. To fix this you can move `/usr/bin` to a higher
  priority in your PATH or you can explicitly call `/usr/bin/find`.
* You may run into line ending errors when trying to apply the patch. Before
  running the command, you can convert the line endings of the files you want
  to edit with `find . -name “name of files” –exec dos2unix {} \;`
* Be very sure to include the `-name` parameter, otherwise find will also
  search file contents, likely finding some of your files in the .git folder
  and modifying them. This can corrupt your git repository.

[vcs-for-papers]: https://academia.stackexchange.com/questions/5277/why-use-version-control-systems-for-writing-a-paper
[git-diff]: https://git-scm.com/docs/git-diff
