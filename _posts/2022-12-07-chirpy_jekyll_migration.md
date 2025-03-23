---
title: Chirpy Jekyll Theme Migration
date: 2022-12-07 20:00:00 -0500
categories: [Jekyll, Chirpy Jekyll Theme]
tags: [Jekyll, Ruby]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

`Minimal Mistake`에서 `Chirpy`로 Migration 하면서 있었던 기록입니다.

## Problem Keywords

- fatal error: 'ruby/config.h' file not found
- Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
- fatal error: 'openssl/ssl.h' file not found
- An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.

## Troubleshoot

1. `chirpy jekyll theme` 프로젝트의 dependencies를 설치해 줍니다. 설치과정에서 해당 애러메세지를 따로 저장해놓지는 못했지만 저와 비슷한 문제로 질문올린 애러메세지를 대신 사용하였습니다.

   > <pre>
   > $ bundle
   > ...
   > ...
   > ...
   > Ignoring ffi-1.9.25 because its extensions are not built.  Try: gem pristine ffi --version 1.9.25
   > Ignoring http_parser.rb-0.6.0 because its extensions are not built.  Try: gem pristine http_parser.rb --version 0.6.0
   > Fetching gem metadata from https://rubygems.org/...........
   > Using rake 10.5.0
   > Using public_suffix 3.0.3
   > Using addressable 2.5.2
   > Using bundler 2.0.1
   > Using colorator 1.1.0
   > Using concurrent-ruby 1.0.5
   > Fetching eventmachine 1.2.7
   > Installing eventmachine 1.2.7 with native extensions
   > Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
   > 
   >     current directory: /Users/name/Desktop/GitHub/blog/vendor/bundle/ruby/2.3.0/gems/eventmachine-1.2.7/ext
   > /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/bin/ruby -r ./siteconf20190212-57509-l19d0i.rb extconf.rb
   > --with-cppflags=-I/usr/local/opt/openssl/include
   > checking for main() in -lcrypto... *** extconf.rb failed ***
   > Could not create Makefile due to some reason, probably lack of necessary
   > libraries and/or headers.  Check the mkmf.log file for more details.  You may
   > need configuration options.
   > 
   > Provided configuration options:
   >     --with-opt-dir
   >     --without-opt-dir
   >     --with-opt-include
   >     --without-opt-include=${opt-dir}/include
   >     --with-opt-lib
   >     --without-opt-lib=${opt-dir}/lib
   >     --with-make-prog
   >     --without-make-prog
   >     --srcdir=.
   >     --curdir
   >     --ruby=/System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/bin/$(RUBY_BASE_NAME)
   >     --with-ssl-dir
   >     --without-ssl-dir
   >     --with-ssl-include
   >     --without-ssl-include=${ssl-dir}/include
   >     --with-ssl-lib
   >     --without-ssl-lib=${ssl-dir}/lib
   >     --with-openssl-config
   >     --without-openssl-config
   >     --with-pkg-config
   >     --without-pkg-config
   >     --with-cryptolib
   >     --without-cryptolib
   > /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:456:in `try_do': The compiler > failed to
   > generate an executable file. (RuntimeError)
   > You have to install development tools first.
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:535:in `block in try_link0'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/tmpdir.rb:89:in `mktmpdir'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:532:in `try_link0'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:556:in `try_link'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:765:in `try_func'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:997:in `block in have_library'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:942:in `block in checking_for'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:350:in `block (2 levels) in postpone'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:320:in `open'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:350:in `block in postpone'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:320:in `open'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:346:in `postpone'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:941:in `checking_for'
   >    from /System/Library/Frameworks/Ruby.framework/Versions/2.3/usr/lib/ruby/2.3.0/mkmf.rb:992:in `have_library'
   >    from extconf.rb:8:in `block in check_libs'
   >    from extconf.rb:8:in `each'
   >    from extconf.rb:8:in `all?'
   >    from extconf.rb:8:in `check_libs'
   >    from extconf.rb:95:in `&lt;main&gt;'
   > 
   > To see why this extension failed to compile, please check the mkmf.log which can be found here:
   > 
   > /Users/name/Desktop/GitHub/blog/vendor/bundle/ruby/2.3.0/extensions/universal-darwin-18/2.3.0/eventmachine-1.2.7/mkmf.log
   > 
   > extconf failed, exit code 1
   > 
   > Gem files will remain installed in
   > /Users/nbame/Desktop/GitHub/blog/vendor/bundle/ruby/2.3.0/gems/eventmachine-1.2.7 for inspection.
   > Results logged to
   > /Users/name/Desktop/GitHub/blog/vendor/bundle/ruby/2.3.0/extensions/universal-darwin-18/2.3.0/eventmachine-1.2.7/gem_make.out
   > 
   > An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.
   > Make sure that `gem install eventmachine -v '1.2.7' --source 'https://rubygems.org/'` succeeds before bundling.
   > 
   > In Gemfile:
   >   jekyll was resolved to 3.8.3, which depends on
   >     em-websocket was resolved to 0.5.1, which depends on
   >       eventmachine
   > </pre>

   - Reference: [How to fix "ERROR: Failed to build gem native extension." when installing eventmachine on macOS Mojave](https://stackoverflow.com/q/54656946)

2. `mkmf.log` 파일을 열어보면 `ruby/config.h` 를 찾을수 없다는 애러메세지를 찾을수 있었습니다.

3. 검색결과 현재 사용하는 `ruby` version이 낮아 문제가 나타나는것 같아 `rvm`를 설치하고 비교적 최신버전인 `3.0.3`로 업그래이드 해줬습니다.

   ```sh
   $ ruby -v
   ruby 2.6.10p210 (2022-04-12 revision 67958) [universal.x86_64-darwin21]
   $ rvm install ruby-3.0.3
   $ ruby -v
   ruby 3.0.3p157 (2021-11-24 revision 3fb7d2cadc) [x86_64-darwin21]
   ```

4. Version upgrade 이후 애러메세지는 이전과는 다를것을 볼수 있었는데, `fatal error: 'openssl/ssl.h' file not found` 라는 메세지가 보입니다.

   > <pre>
   > Gem::Ext::BuildError: ERROR: Failed to build gem native extension.
   > 
   >     current directory: /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/gems/3.0.0/gems/eventmachine-1.2.7/ext
   > /Users/username/.rvm/rubies/ruby-3.0.3/bin/ruby -I /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0 -r ./siteconf20221207-37422-o5ha6q.rb extconf.rb
   > checking for -lcrypto... yes
   > checking for -lssl... yes
   > checking for openssl/ssl.h... yes
   > checking for openssl/err.h... yes
   > checking for rb_trap_immediate in ruby.h,rubysig.h... no
   > checking for rb_thread_blocking_region()... no
   > checking for rb_thread_call_without_gvl() in ruby/thread.h... yes
   > checking for rb_thread_fd_select()... yes
   > checking for rb_fdset_t in ruby/intern.h... yes
   > checking for rb_wait_for_single_fd()... yes
   > checking for rb_enable_interrupt()... no
   > checking for rb_time_new()... yes
   > checking for inotify_init() in sys/inotify.h... no
   > checking for __NR_inotify_init in sys/syscall.h... no
   > checking for writev() in sys/uio.h... yes
   > checking for pipe2() in unistd.h... no
   > checking for accept4() in sys/socket.h... no
   > checking for SOCK_CLOEXEC in sys/socket.h... no
   > checking for sys/event.h... yes
   > checking for sys/queue.h... yes
   > checking for clock_gettime()... yes
   > checking for CLOCK_MONOTONIC_RAW in time.h... yes
   > checking for CLOCK_MONOTONIC in time.h... yes
   > CXXFLAGS= -Wall -Wextra -Wno-deprecated-declarations -Wno-ignored-qualifiers -Wno-unused-result -Wno-address
   > creating Makefile
   > 
   > current directory: /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/gems/3.0.0/gems/eventmachine-1.2.7/ext
   > make DESTDIR\= clean
   > 
   > current directory: /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/gems/3.0.0/gems/eventmachine-1.2.7/ext
   > make DESTDIR\=
   > compiling binder.cpp
   > In file included from binder.cpp:20:
   > ./project.h:119:10: fatal error: 'openssl/ssl.h' file not found
   > #include &lt;openssl/ssl.h&gt;
   >         ^~~~~~~~~~~~~~~
   > 1 error generated.
   > make: *** [binder.o] Error 1
   > 
   > make failed, exit code 2
   > 
   > Gem files will remain installed in /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/gems/3.0.0/gems/eventmachine-1.2.7 for inspection.
   > Results logged to /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/gems/3.0.0/extensions/x86_64-darwin-21/3.0.0/eventmachine-1.2.7/gem_make.out
   > 
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:93:in `run'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:44:in `block in make'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:36:in `each'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:36:in `make'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/ext_conf_builder.rb:63:in `block in build'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/tempfile.rb:317:in `open'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/ext_conf_builder.rb:26:in `build'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:159:in `build_extension'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:193:in `block in build_extensions'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:190:in `each'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/ext/builder.rb:190:in `build_extensions'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/rubygems/installer.rb:845:in `build_extensions'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/rubygems_gem_installer.rb:71:in `build_extensions'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/rubygems_gem_installer.rb:28:in `install'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/source/rubygems.rb:200:in `install'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/installer/gem_installer.rb:54:in `install'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/installer/gem_installer.rb:16:in `install_from_spec'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/installer/parallel_installer.rb:186:in `do_install'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/installer/parallel_installer.rb:177:in `block in worker_pool'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/worker.rb:62:in `apply_func'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/worker.rb:57:in `block in process_queue'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/worker.rb:54:in `loop'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/worker.rb:54:in `process_queue'
   >   /Users/username/.rvm/rubies/ruby-3.0.3/lib/ruby/3.0.0/bundler/worker.rb:91:in `block (2 levels) in create_threads'
   > 
   > An error occurred while installing eventmachine (1.2.7), and Bundler cannot continue.
   > 
   > In Gemfile:
   >   jekyll-theme-chirpy was resolved to 5.3.2, which depends on
   >     jekyll-archives was resolved to 2.2.1, which depends on
   >       jekyll was resolved to 4.3.1, which depends on
   >         em-websocket was resolved to 0.5.3, which depends on
   >           eventmachine
   > </pre>

5. 아래의 옵션값과 함께 `eventmachine`를 설치해 줍니다.

   ```sh
   $ sudo gem install eventmachine -v '1.2.7' -- --with-openssl-dir=/usr/local/opt/openssl@3
   ```

## Reference

- [macOS Mojave 'ruby/config.h' file not found](https://stackoverflow.com/a/65399187)
- [eventmachine does not compile with ruby 3.0.0preview1 on macOS](https://github.com/eventmachine/eventmachine/issues/932#issuecomment-1277726994)
