# Rolling a release

## Prep the release

* Update version number in `lib/sugarjar/version.rb`
* Update the `CHANGELOG.md`
* Create a PR, get it merged

## Tag the release

* version='0.0.X'
* Add a tag: `git tag -a v${version?} -m "version ${version?}" -s`
* Push the tag: `git push origin --tags`

## Publish a gem

* Build a gem: `gem build sugarjar.gemspec`
* Push the gem: `gem push sugarjar-${version?}.gem`

## Publish omnibus builds

* From omnibus directory, prep: `bundle install`
* Inside of each VM...

  ```shell
  for d in ubuntu-1804 ubuntu-2004 debian-11 centos-7 centos-8 fedora-31; do
    bundle exec kitchen converge default-$d && \
      bundle exec kitchen login default-$d && \
      bundle exec kitchen destroy default-$d
  done
  ```

  * Do a build...

    (for fedora you'll need to `sudo dnf install rpm-build`)

    ```shell
    .  load-omnibus-toolchain.sh
    [ -e .bundle ] && sudo chown -R vagrant:vagrant .bundle
    cd sugarjar/omnibus
    bundle install
    bin/omnibus build sugarjar && \
      bin/omnibus clean sugarjar # required so next build works
    ```

  * Grab/rename the package out of sugarjar/omnibus/pkg

## Publish Fedora builds

See `packaging/README.md`.
