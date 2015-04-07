---
layout: post
title:  "Installing OCaml on CentOS"
date:   2015-04-02 7:00:00
categories: "software engineering"
tags: ["ocaml", "software engineering", "functional programming"]
---

I recently decided to learn more about functional programming by developing a few projects in OCaml and reading [Real World OCaml](https://realworldocaml.org/). Setting up the OCaml environment proved to be a bit of a challenge, however.

For my environment, I'm using the OCaml compiler, `ocamlbuild` tool, [OPAM](https://opam.ocaml.org/) package manager, and several libraries including Jane Street Capital's Core library.  I'm using the [taureg](https://github.com/ocaml/tuareg) emacs mode for editing.

The first step is to install OPAM from packages provided by the OpenSUSE developers:

    $ sudo yum install yum-utils
    $ sudo yum-config-manager --add-repo http://download.opensuse.org/repositories/home:ocaml/CentOS_7/home:ocaml.repo
    $ sudo yum install opam
	$ opam init
    $ eval `opam config env`

Originally, I made the mistake of installing the `ocaml` packages through `yum`. However, I ran into compatibility issues with `opam` when trying to install `camlp4`.  Instead, you should use `opam` to install the compiler:

    $ opam switch 4.02.1

Building and installing the compiler takes a while.  The next step is to install libraries suggested by Real World OCaml:

    $ opam install core utop
	$ opam install async yojson core_extended core_bench cohttp async_graphics cryptokit menhir

To set up the `utop` interactive environment, add the following text to `~/.ocamlinit`:

    #use "topfind";;
    #thread;;
    #camlp4o;;
    #require "core.top";;
    #require "core.syntax";;

You should now be able to run `utop` and build projects using `ocamlbuild`.  As I make more progress, I'll post on organizing and building OCaml projects.
