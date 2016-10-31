###Create gpg keys

Use gpg command to create a gpg key for you pkg signature.

    mkdir ~/.gnupg (maybe it's optional)
    gpg --gen-key
    gpg --list-keys

###Config your RPM macros

Edit ~/.rpmmacros, add your gpg key configuration.

    %_signature gpg
    %_gpg_path PATH_TO_.gnupg
    %_gpg_name YOUR NAME
    %_gpgbin /usr/bin/gpg


###Build pkgs with sign

build pkgs

    rpmbuild -v -ba --sign --clean SPECS/your.spec

###Export gpg public key

Export your gpg public key so that target OS can import it to verfy pkgs published by you.

    gpg --armor --output YOUR-GPG-KEY --export 'YOUR NAME'

###Import Your gpg key

Import gpg key to verfy installed pkgs.

    rpm --import YOUR-GPG-KEY

###Refs

References:[1],   [2]

  [1]: http://www.rpm.org/max-rpm/ch-rpm-pgp.html
  [2]: https://www.gnupg.org/gph/en/manual.html
