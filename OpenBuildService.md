#### Git Repo
https://github.com/pharo-project/pharo-openbuildservice

#### Spec file (see [reference](http://fedoraproject.org/wiki/How_to_create_an_RPM_package#Building_the_binary_package))

So far, the it's working up through the %build step. I'm not sure what to do for %install. I tried something naive, just as a proof-of-concept:
```
%install
rm -rf %{buildroot}
mkdir -p %{buildroot}%{_bindir}/pharo-vm
cp -p results/* %{buildroot}%{_bindir}/pharo-vm
```
but I got:
```
ERROR   0020: file '/usr/bin/pharo-vm/libSDL2DisplayPlugin.so' contains an rpath referencing '..' of an absolute path [/root/rpmbuild/BUILD/pharo-4.0/build/../results]
error: Bad exit status from /var/tmp/rpm-tmp.Nt0jZo (%install)
```
