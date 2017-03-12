# git-sign: sign files in Git repositories securely

While Git supports signing via `git tag -s` (or `git tag -u`), those
are signing SHA-1 hashes only as of today, hence a bit insecure given
that SHA-1 collisions aren't very expensive (about $75k according to
recent [news](https://duckduckgo.com/html/?q=sha-1%20collisions); a
SHA-1 collision still doesn't allow to MITM directly (pre-image attack
would be needed), but given the right circumstances could still make
attacks easier).

`git-sign` is a wrapper that adds SHA-256 hashes of all files to the
tag message in a way that can either be verified manually (the code
used to generate the hashes is added to the tag message as well), or
automatically using `verify-sig`.

The two programs are simple bash scripts so that you can verify their
source code easily. Also, the signed tags mention the code to run,
hence users can verify them without even installing verify-sig.

Note that while this ensures that the checked-out files (in the
working directory) are of identical number and content as those the
author (signatory) had, the Git history might still have been
subverted by an attacker. In other words, `verify-sig` will give you
assurance when building a checkout of a signed commit that there's no
man in the middle, you don't get any assurance that any other commits
contain what the author committed (not even older commits). For this,
Git itself has to move to a more secure hashing algorithm.

## Usage

* `git-sign v10`, `git-sign v10 "release 10"`, `GITSIGNKEY=04EDB072
  git-sign v10`: create a Git tag named `v10` on the current HEAD,
  adding hashes of all files in the current working directory. See
  `git-sign -h` for more details. Note that it uses hashes of the
  files in the working directory, not those stored in Git's HEAD, thus
  you need to run this on a clean working directory (new files not
  added to the index don't hurt, but deletions or modifications
  do). Just run immediately after a `git commit -a`, or verify with
  `git status` that the working directory is clean.

* `verify-sig v10`: verify the signature and file hashes on the tag
  `v10`. Similarly to `git-sign`, this assumes that the working
  directory contains the same files that the tag being checked refers
  to. Otherwise `verify-sig` will report the files that are different
  between the tag and HEAD or the working directory. To get clean
  verification, first check out the version pointed to by the tag that
  you want to verify, e.g. with `git checkout -b v10 v10` (or just
  `git checkout v10` if you're not confused by working with a detached
  HEAD).


## TODO?

Only the contents of the files is verified, not their executable
status. Also, symlinks are resolved before hashing, which may or may
not be the best idea. Changing these would make the scripts
considerably more complex, though.


## See also

* [git-evtag](https://github.com/cgwalters/git-evtag): much more
  complex; easy verifiability of the source code of the checker, or
  even just manual checking, was the aim for git-sign.

* [git-secure-tag](https://github.com/indutny/git-secure-tag): in
  Node.js, also much more complex.


## LICENSE

This software is licensed under the MIT License.

Copyright Christian Jaeger, 2017.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
