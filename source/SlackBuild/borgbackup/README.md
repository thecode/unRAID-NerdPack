BorgBackup (short: Borg) is a deduplicating backup program.
Optionally, it supports compression and authenticated encryption.

The main goal of Borg is to provide an efficient and secure way to
backup data. The data deduplication technique used makes Borg suitable
for daily backups since only changes are stored. The authenticated
encryption technique makes it suitable for backups to not fully trusted
targets.

Borg is a fork of Attic and is maintained by "The Borg collective".

Optional dependencies:
- llfuse (https://slackbuilds.org/apps/llfuse/) is required,
  if you wish to mount an archive as a FUSE filesystem,
- Sphinx (https://slackbuilds.org/apps/Sphinx/) and
  sphinx_rtd_theme (https://slackbuilds.org/apps/sphinx_rtd_theme/)
  are required if you wish to build the docs (man, html).
  Please note that the build will fail if Sphinx is installed,
  but sphinx_rtd_theme is not.



## kubedzero build & execute notes

- Since the SlackBuild file only calls for the `slack-desc`, `borgbackup-1.1.x.tar.gz`, and the `unicode_docs.patch` file, I've removed the `borgbackup.info` file since it was out of date anyway.
- https://github.com/borgbackup/borg/issues/5591 we found in BorgBackup-1.1.15 that xxHash-0.8.0 is now preferred as the hash library instead of a built-in hash library. That means it depends on files not installed as part of the BorgBackup package installation. During compilation of BorgBackup, it checks a Boolean https://github.com/borgbackup/borg/blob/1.1.15/setup.py#L26 and also looks for installed copies of xxHash (and its library libxxhash) to determine if this "better" library should be used or if the bundled hash library should be used. If 
- This same "check the system if this package is installed, otherwise use the bundled version" applies to `lz4`, `libstd`, and `libb2` (blake2). It should be noted that if BorgBackup is compiled while the system library is used but the compiled package is then moved to a system such as Unraid without that installed package, an error such as `libxxhash.so.0: cannot open shared object file: No such file or directory` may be seen. This particular error was seen when running `borg --version` on a system without the `xxhash` library installed. I downloaded the library from a Slackware source mirror: http://slackware.cs.utah.edu/pub/slackware/slackware64-current/slackware64/l/ or https://mirrors.slackware.com/slackware/slackware64-current/slackware64/l/
- I updated the SlackBuild to adjust these flags in the `setup.py` file to False (default is True) to force usage of the bundled libraries for maximum portability between Slackware and the super-cut-down Unraid flavor of Slackware. This way, Unraid users of this package need not install extra packages such as `xxHash` to get BorgBackup working.
- There were some other files in this SlackBuild directory:
  - `acl-2.2.53-x86_64-1.txz` - Retrieved from the Slackware A package group. I had this installed when compiling, but did not have this installed on Unraid
  - `libffi-3.3-x86_64-1.txz` - Retrieved from the Slackware L package group. I had this installed when compiling, and also have it installed in Unraid for an unrelated project `openVMTools_compiled`. I believe it is installed via Nerd Pack on Unraid. 
  - `lz4-1.9.3-x86_64-1.txz` - Retrieved from the Slackware L package group. I had this installed when compiling, but did not have this installed on Unraid
  - `msgpack-python-0.6.2-x86_64-2cf.txz` - Not sure what this is or where this is from. I did not have this installed when compiling, nor did I have this on Unraid. I checked in `pip3 --list` as well and confirmed Python3 is not aware of it. 
  - `cryptography-2.8-x86_64-1cf.txz` - Not sure what this is or where this is from. Not installed during compilation, nor is it installed on Unraid
  - NOTE: Since we disabled the flag to use the system hash library, `xxHash-0.8.0-x86_64-1.txz` is not needed and therefore is not included in this directory. It can be retrieved from the Slackware L package group if need be. 
- The original README noted that, in order for documentation to be generated, both Sphinx and `sphinx_rtd_theme` needed to be installed. However, during compilation I ran into an error `No module named 'guzzle_sphinx_theme'` and then also needed to install https://pypi.org/project/guzzle_sphinx_theme/ with `pip3 install guzzle_sphinx_theme`. After that, I was able to complete the compilation.
- My list of Pip3 packages (with Python 3.9.1) at the time of compilation was `-lxc=0.1, alabaster=0.7.12, appdirs=1.4.4, Babel=2.9.0, Brotli=1.0.9, caca=0.99b19, certifi=2020.12.5, chardet=4.0.0, cupshelpers=1.0, Cython=0.29.21, distro=1.5.0, docutils=0.16, future=0.18.2, getmail6=6.11, gpg=1.15.0, guzzle-sphinx-theme=0.7.11, gyp=0.1, idna=2.10, imagesize=1.2.0, include-server=3.3.3, isc=2.0, Jinja2=2.11.2, lensfun=0.3.95, libwebp=0.0, llfuse=1.3.8, M2Crypto=0.37.1, Mako=1.1.3, MarkupSafe=1.1.1, mercurial=5.6.1, meson=0.56.0, netsnmp-python=1.0a1, notify2=0.3.1, packaging=20.8, Pillow=8.0.1, pip=20.3.3, ply=3.11, pssh=2.3.4, pwquality=1.4.4, pycairo=1.20.0, pycups=2.0.1, pycurl=7.43.0.6, Pygments=2.7.3, PyGObject=3.38.0, pyparsing=2.4.7, PyQt4-sip=4.19.23, PyQt5=5.15.2, PyQt5-sip=12.8.1, pysol-cards=0.10.1, python-sane=2.8.3, pytz=2020.5, pyxdg=0.27, QScintilla=2.11.6, random2=1.0.1, requests=2.25.1, rpm=4.16.1.2, SCons=4.0.1, setuptools=51.1.0, sip=5.5.0, six=1.15.0, snowballstemmer=2.0.0, Sphinx=3.4.1, sphinx-rtd-theme=0.5.0, sphinxcontrib-applehelp=1.0.2, sphinxcontrib-devhelp=1.0.2, sphinxcontrib-htmlhelp=1.0.3, sphinxcontrib-jsmath=1.0.1, sphinxcontrib-qthelp=1.0.3, sphinxcontrib-serializinghtml=1.1.4, toml=0.10.2, undervolt=0.3.0, urllib3=1.26.2`