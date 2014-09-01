# org-passwords.el 

A password manager for [Org](http://orgmode.org/).

## Overview

It is safer to keep a different password for each website that
requires a password. That way, if a website gets compromised, then all
your other logins are safe. With the astronomical amount of websites
that are accessed by a standard person, it is impossible to remember
all your passwords. A password manager provides a "one password to
rule them all" approach. In this case, an Org file is protected with
the-one password (the only one to remember) and contains all the other
passwords (and secrets). The entries in that file can be for example
of the form:

	* My favorite lol-cat website
      :PROPERTIES:
	  :URL: http://www.myfavoritelolcat.com
      :USERNAME: jorge
      :PASSWORD: 123456
      :END:
	  
	  Some notes about the website that will help to find the entry
      fast with a C-s.

But you can keep more than that, for example:

	* Cards
	** My main credit card
	   :PROPERTIES:
	   :NAMEONCARD: Jorge Alfaro-Murillo
	   :NUMBER:     1234000000005678
	   :EXPIRES:    11/19
	   :PIN:        1234
	   :CVR:        777
	   :END:

	** My main debit card
	   :PROPERTIES:
	   :NAMEONCARD: Jorge Alfaro-Murillo
	   :NUMBER:     4321000000005678
	   :EXPIRES:    12/19
	   :PIN:        1234
	   :CVR:        888
	   :END:

org-passwords gives a mode derived from Org to consult the database
file and copy passwords, and functions for generating the passwords.
In particular, for passwords that you DO need to remember (but might
forget so still want to include in the database) it can create
passwords of the
[correcthorsebatterystaple](http://xkcd.com/936/)-type.

## Basic setup

Start out by defining your passwords file and a dictionary file, for
example:

	(require 'org-passwords)
	(setq org-passwords-file "~/documents/passwords.gpg")
	(setq org-passwords-random-words-dictionary "/etc/dictionaries-common/words")

The last line points to the dictionary (a file in which each line is a
word), the one given above is standard for GNU/Linux systems.

## Usage

### Consulting passwords

To consult your database of passwords use

	M-x org-passwords

This opens the 'org-passwords-file' in Read-Only mode (to not mess
with it if you are just consulting the file), and sets a timer of one
minute after which the buffer of the file is automatically killed. The
timer can be adjusted with the variable 'org-passwords-time-opened'.

After this, do a C-s to find your entry, and copy the password to the
system clipboard (or pasteboard in Mac):

	M-x org-passwords-copy-password

This copies the password, but does not add it to the kill-ring, that
way your password is not compromised if later a bad guy that knows
what C-y M-y does, grabs your computer.

If you also forget what your user name is, there is also:

	M-x org-passwords-copy-username

And very useful for accessing the website (in case there is one):

	M-x org-passwords-open-url

Note that by default Org does not show the PROPERTIES, so you (or the
person peeking at your computer) never have to see the actual
password.

If you want to be able to display less sensitive properties while
still hiding passwords, you could also use different drawers to store
them:

	* Cards
	** My main credit card
	   :PROPERTIES:
	   :NAMEONCARD: Jorge Alfaro-Murillo
	   :NUMBER:     1234000000005678
	   :EXPIRES:    11/19
	   :CVR:        777
	   :END:
       
       :HIDDEN:
	   :PIN:        1234
       :END:

You also need to define this new drawer at the top of the file:

    #+drawers: PROPERTIES HIDDEN

## Generating passwords

### Passwords that you won't have to remember

To generate a random string of numbers, lowercase letters and
uppercase letters use:

	M-x org-passwords-generate-password

To generate a a random string of numbers, lowercase letters, uppercase
letters and symbols use:

	C-u M-x org-passwords-generate-password

Both commands will ask for a length of the password. The latter is, of
course, safer but it is common for websites to not allow symbols in a
password.

### Passwords to remember

To concatenate random words from the dictionary defined by
'org-passwords-random-words-dictionary' use:

	M-x org-passwords-random-words

It will ask for the number of words wanted. Each word will be
separated by the string defined in
'org-passwords-random-words-separator', which by default is "-".

The following does the same, but makes substitutions in the password:

	C-u M-x org-passwords-random-words

The substitutions are made according to the variable
'org-passwords-random-words-substitutions', which is a list of pairs
of strings of the form ("substitute-this" . "by-this"). The default is
given as

	(("a" . "@") ("e" . "3") ("o" . "0"))

So the "correcthorsebatterystaple" would be instead
"c0rr3cth0rs3b@tt3ryst@pl3". Note: I use another system, this is just
an idea and you are encouraged to change the variable, do:

	(setq org-passwords-random-words-substitutions
	      '(("for" . "4") ("s" . "5")))

## Tips and tricks

### Key bindings

It is also useful to set up keybindings for the functions
'org-passwords-copy-username' and 'org-passwords-copy-password' in the
'org-passwords-mode', to easily make the passwords and usernames
available to the facility for pasting text. You can set for example:

    (eval-after-load "org-passwords"
      '(progn
         (define-key org-passwords-mode-map
           (kbd "C-c u")
           'org-passwords-copy-username)
         (define-key org-passwords-mode-map
           (kbd "C-c p")
           'org-passwords-copy-password)))

### Making new entries in the database

To enter new passwords, you can use 'org-capture' and a minimal template like:
         
    ("p" "password" entry (file "~/documents/passwords.gpg")
     "* %^{Title}\n  %^{PASSWORD}p %^{USERNAME}p")

When asked for the password you can then call either
'org-passwords-generate-password' or 'org-passwords-random-words'.

Be sure to enable recursive minibuffers to call those functions from
the minibuffer:

    (setq enable-recursive-minibuffers t)

## Security

Of course, keep your main password file, encrypted. In emacs it is
enough to for the file to have the file end in .gpg, emacs will tell
you what to do when saving it. Keep this file in an encrypted drive,
do not write the password to your main password file in a post-it and
paste it your desk, do not use "password" or "12345" as your password,
etc.

## License

Copyright (C) 2014 Jorge A. Alfaro-Murillo

Author: Jorge A. Alfaro-Murillo <jorge.alfaro-murillo@yale.edu>

Keywords: password, passwords

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or (at
your option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
General Public License for more details.

You should have received a copy of the GNU General Public License
along with GNU Emacs. If not, see <http://www.gnu.org/licenses/>.
