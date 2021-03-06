httpclient - HTTP accessing library.
Copyright (C) 2000-2010  NAKAMURA, Hiroshi  <nahi@ruby-lang.org>.

'httpclient' gives something like the functionality of libwww-perl (LWP) in
Ruby.  'httpclient' formerly known as 'http-access2'.

See HTTPClient for documentation.


== Features

* methods like GET/HEAD/POST/* via HTTP/1.1.
* HTTPS(SSL), Cookies, proxy, authentication(Digest, NTLM, Basic), etc.
* asynchronous HTTP request, streaming HTTP request.

* by contrast with net/http in standard distribution;
  * Cookies support
  * MT-safe
  * streaming POST (POST with File/IO)
  * Digest auth
  * Negotiate/NTLM auth for WWW-Authenticate (requires net/htlm module; rubyntlm gem)
  * NTLM auth for Proxy-Authenticate (requires 'win32/sspi' module; rubysspi gem)
  * extensible with filter interface
  * you don't have to care HTTP/1.1 persistent connection
    (httpclient cares instead of you)

* Not supported now
  * Cache
  * Rather advanced HTTP/1.1 usage such as Range, deflate, etc.
    (of course you can set it in header by yourself)


== Author

Name:: NAKAMURA, Hiroshi
E-mail:: nahi@ruby-lang.org
Project web site:: http://github.com/nahi/httpclient


== License

This program is copyrighted free software by NAKAMURA, Hiroshi.  You can
redistribute it and/or modify it under the same terms of Ruby's license;
either the dual license version in 2003, or any later version.

httpclient/session.rb is based on http-access.rb in http-access/0.0.4.
Some part of code in http-access.rb was recycled in http-access2.rb.
Those part is copyrighted by Maehashi-san who made and distributed
http-access/0.0.4. Many thanks to Maehashi-san.


== Install

  $ ruby install.rb

It will install lib/* to your site_ruby directory such as
/usr/local/lib/ruby/site_ruby/1.8/.


== Uninstall

Delete installed files from your site_ruby directory.


== Usage

See HTTPClient for documentation.
You can also check sample/howto.rb how to use APIs.


== Download

* Stable: http://dev.ctor.org/download/httpclient-2.1.6.tar.gz (tar + gzip)
* Stable: http://dev.ctor.org/download/httpclient-2.1.6.zip (ZIP) 

* Older versions: http://dev.ctor.org/download/archive/ 

* Gem repository for stable version
  * (at default remove source at rubyforge.org) 

* git: git://github.com/nahi/httpclient.git

=== Gem

You can install httpclient with rubygems.

  % gem install httpclient


== Bug report or Feature request

Please file a ticket at the project web site.

1. find a similar ticket from https://github.com/nahi/httpclient/issues
2. create a new ticket by clicking 'Create Issue' button.
3. you can use github features such as pull-request if you like.

Thanks in advance. 


== Changes


= Changes in 2.1.6 =

  Dec 20, 2010 - version 2.1.6

    * IMPORTANT update for HTTPS(SSL) connection
      * Trusted CA bundle file cacert_sha1.p7s for older environment (where
        you cannot use SHA512 algorithm such as an old Mac OS X) included in
        httpclient 2.1.5 expires in Dec 31, 2010.  Please update to 2.1.6 if
        you're on such an environment.
      * Updated trusted CA certificates file (cacert.p7s and cacert_sha1.p7s).
        CA certs are imported from
        'Java(TM) SE Runtime Environment (build 1.6.0_22-b04)'. 

    * IMPORTANT bug fix for persistent connection
      * #29 Resource Leak: If httpclient establishes two connections to the
        same server in parallel, one of these connections will be leaked, patch
        by xb.
      * #30 When retrying a failed persistent connection, httpclient should use
        a fresh connection, reported by xb.
        These 2 fixes should fix 'Too many open files' error as well if you're
        getting this. Please check 2.1.6 and let me know how it goes!

    * Features
      * #4 Added OAuthClient. See sample clients in sample/ dir.
      * #42 Added transparent_gzip_decompression property, patch by Teshootub7.
        All you need to use it is done by;
        client.transparent_gzip_decompression = true
        Then you can retrieve a document as usural in decompressed format.
      * #38 Debug dump binary data (checking it includes \0 or not) in hex
        encoded format, patch by chetan.

    * Bug fixes
      * #8 Opened certificate and key files for SSL not closed properly.
      * #10 "get" method gets blocked in "readpartial" when receiving a 304
        with no Content-Length.
      * #11 Possible data corruption problem in asynchronous methods, patch by
        a user. (http://dev.ctor.org/http-access2/ticket/228)
      * #13 illegal Cookie PATH handling. When no PATH part given in Set-Cookie
        header, URL's path part should be used for path variable.
      * #16 httpclient doesn't support multiline server headers.
      * #19 set_request_header clobbers 'Host' header setting if given, patch
        by meuserj.
      * #20 Relative Location on https redirect fails, patch by zenchild.
      * #22 IIS/6 + MicrosoftSharePointTeamServices uses "NTLM" instead of
        "Negotiate".
      * #27 DigestAuth header: 'qop' parameter must not be enclosed between
        double quotation, patch by ibc.
      * #36 Wrong HTTP version in headers with Qt4 applications, reported by
        gauleng.
      * #38 DigestAuth + posting IO fails, patch by chetan.
      * #41 https-over-proxy fails with IIS, patch by tai.

= Changes in 2.1.5 =

  Jun 25, 2009 - version 2.1.5.2

    * Added another cacert distribution certificate which uses
      sha1WithRSAEncryption.  OpenSSL/0.9.7 cannot handle non-SHA1 digest
      algorithm for certificate.  The new certificate is
      RSA 2048 bit + SHA1 + notAfter:2010/12/31.  Corresponding CA bundle file
      is cacert_sha1.p7s.  It is loaded only when cacert.p7s cannot be loaded
      with the original distribution certificate.

  Jun 11, 2009 - version 2.1.5.1

    * README update.

  Jun 8, 2009 - version 2.1.5

    * IMPORTANT update for HTTPS(SSL) connection
      * Trusted CA bundle file included in httpclient <= 2.1.4 expires in
        Nov 2009. Please update to 2.1.5 by Oct 2009 if your application
        depends on trusted CA bundle file.
      * Updated trusted CA certificates file (cacert.p7s). CA certs are
        imported from 'Java(TM) SE Runtime Environment (build 1.6.0_13-b03)'. 
      * Updated a cacert distribution certificate.
        RSA 2048 bit + SHA512 + notAfter:2037/12/31. (#215)

    * Feature
      * WWW authentication with Negotiate based on win32/sspi as same as Proxy
        authentication. Applied a patch from Paul Casto. Thanks! (#212)

    * Bug fixes
      * Infinite loop caused by EOF error while reading response message body
        without Content-Length.  IO#readpartial does not clear the second
        argument (buffer) when an exception raised.  Fixed by a patch from an
        user.  Thanks! (#216)
      * NoMethodError caused by the cookie string that includes a double
        semicolons ";;".  Fixed by a patch from an user.  Thanks! (#211)
      * CNONCE attribute in Digest Authentication was not properly generated by
        itself (used same nonce sent from the connecting server). Fixed by a
        patch from bterlson
        [http://github.com/bterlson/httpclient/commit/6d0df734840985a7be88a2d54443bbf892d50b9a]
        Thanks! (#209)
      * Cookie header was not set in authentication negotiation. Fixed. This
        bug was found and pointed out by bterlson at
        [http://github.com/bterlson/httpclient/commits/master]. Thanks! (#210)
      * Do not send 'Content-Length: 0' when a request doesn't have message
        body. Some server application (!EasySoap++/0.6 for example) corrupts
        with the request with Content-Length: 0. This bug was found by clay
        [http://daemons.net/~clay/2009/05/03/ruby-why-do-you-torment-me/].
        Thanks! (#217)
      * Ensure to reset connection after invoking HTTPClient singleton methods
        for accessing such as HTTPClient.get_content. Thanks to @xgavin! (#214)

  Feb 13, 2009 - version 2.1.4

    * Bug fixes
      * When we hit some site through http-proxy we get a response without
        Content-Length header.  httpclient/2.1.3 drops response body for such
        case. fixed. (#199)
      * Avoid duplicated 'Date' header in request. Fixed. (#194)
      * Avoid to add port number to 'Host' header.  Some servers like GFE/1.3
        dislike it. Thanks to anonymous user for investigating the behavior.
        (#195)
      * httpclient/2.1.3 does not work when you fork a process after requiring
        httpclient module (Passenger). Thanks to Akira Yamada for tracing down
        this bug. (#197)
      * httpclient/2.1.3 cannot handle Cookie header with 'expires=' and
        'expires=""'.  Empty String for Time.parse returns Time.now unlike
        ParseDate.parsedate. Thanks to Mark for the patch. (#200) 

  Jan 8, 2009 - version 2.1.3.1

    * Security fix introduced at 2.1.3.
      * get_content/post_content of httpclient/2.1.3 may send secure cookies
        for a https site to non-secure (non-https) site when the https site
        redirects the request to a non-https site.  httpclient/2.1.3 caches
        request object and reuses it for redirection.  It should not be cached
        and recreated for each time as httpclient <= 2.1.2 and http-access2.
      * I realized this bug when I was reading open-uri story on
        [ruby-core:21205].  Ruby users should use open-uri rather than using
        net/http directly wherever possible.

  Dec 29, 2008 - version 2.1.3

    * Features
      * Proxy Authentication for SSL.
      * Performance improvements.
      * Full RDoc. Please tell me any English problem. Thanks in advance.
      * Do multipart file upload when a given body includes a File. You don't
        need to set 'Content-Type' and boundary String any more.
      * Added propfind and proppatch methods. 

    * Changes
      * Avoid unnecessary memory consuming for get_content/post_content with
        block.  get_content returns nil when you call it with a block.
      * post_content with IO did not work when redirect/auth cycle is required.
        (CAUTION: post_content now correctly follows redirection and posts the
        given content)
      * Exception handling cleanups.
        * Raises HTTPClient::ConfigurationError? for environment problem.
          (trying to do SSL without openssl installed for example)
        * Raises HTTPClient::BadResponse? for HTTP response problem.  You can
          get the response HTTPMessage returned via $!.res.
        * Raises SocketError? for connection problem (as same as before). 

    * Bug fixes
      * Avoid unnecessary negotiation cycle for Negotiate(NTLM) authentication.
        Thanks Rishav for great support for debugging Negotiate authentication.
      * get_content/post_content with block yielded unexpected message body
        during redirect/auth cycle.
      * Relative URI redirection should be allowed from 2.1.2 but it did not
        work... fixed.
      * Avoid unnecessary timeout waiting when no message body returned such as
        '204 No Content' for DAV.
      * Avoid blocking on socket closing when the socket is already closed by
        foreign host and the client runs under MT-condition. 

  Sep 22, 2007 - version 2.1.2

    * HTTP
      * implemented Negotiate authentication with a support from exterior
        modules. 'rubyntlm' module is required for Negotiate auth with IIS.
        'win32/sspi' module is required for Negotiate auth with ISA.
      * a workaround for Ubuntu + SonicWALL timeout problem. try to send HTTP
        request in one chunk.

    * SSL
      * create new self-signing dist-cert which has serial number 0x01 and
        embed it in httpclient.rb.
      * update cacert.p7s. certificates are imported from cacerts in JRE 6
        Update 2. 1 expired CA certificate
        'C=US, O=GTE Corporation, CN=GTE CyberTrust Root' is removed.

    * Bug fix
      * [BUG] SSL + debug_dev didn't work under version 2.1.1.
      * [BUG] Reason-Phrase of HTTP response status line can be empty according
      * to RFC2616.

  Aug 28, 2007 - version 2.1.1

    * bug fix
      * domain_match should be case insensitive. thanks to Brian for the patch.
      * before calling SSLSocket#post_connection_check, check if
        RUBY_VERSION > "1.8.4" for CN based wildcard certificate. when
        RUBY_VERSION <= "1.8.4",  it fallbacks to the post_connection_check
        method in HTTPClient so httpclient should run on 1.8.4 fine as before.

    * misc
      * added HTTPClient#test_loopback_http_response which accepts test
        loopback response which contains HTTP header. 

  Jul 14, 2007 - version 2.1.0

    * program/project renamed from 'http-access2' to 'httpclient'.
      there's compatibility layer included so existing programs for
      http-access2 which uses HTTPAccess2::Client should work with
      httpclient/2.1.0 correctly.

    * misc
      * install.rb did not install cacerts.p7s.  Thanks to knu.
      * now HTTPClient loads http_proxy/HTTP_PROXY and no_proxy/NO_PROXY
        environment variable at initialization time. bear in mind that it
        doesn't load http_proxy/HTTP_PROXY when a library is considered to be
        running under CGI environment (checked by ENVREQUEST_METHOD existence.
        cgi_http_proxy/CGI_HTTP_PROXY is loaded instead.

  Jul 4, 2007 - version 2.0.9

    * bug fix
      * fix the BasicAuth regression problem in 2.0.8.  A server may return
        "BASIC" as an authenticate scheme label instead of "Basic".  It must be
        treated as a case-insensitive token according to RFC2617 section 1.2.
        Thanks to mwedeme for contributing the patch. (#159)

  Jun 30, 2007 - version 2.0.8

    * HTTP
      * added request/response filter interface and implemented DigestAuth
        based on the filter interface.  DigestAuth calc engine is based on
        http://tools.assembla.com/breakout/wiki/DigestForSoap
        Thanks to sromano. (#155)
      * re-implemented BasicAuth based on the filter interface.  send BasicAuth
        header only if it's needed. (#31)
      * handle a response which has 2XX status code as a successfull response
        while retry check.  applied the patch from Micah Wedemeyer.
        Thanks! (#158)

    * Connection
      * show more friendly error message for unconnectable URL. (#156)

    * bug fixes
      * to avoid MIME format incompatibility, add empty epilogue chunk
        explicitly.  Thanks to the anonymous user who reported #154 (#154)
      * rescue EPIPE for keep-alive reconnecting.  Thanks to anonymous user
        who posted a patch at #124. (#124)

  May 13, 2007 - version 2.0.7

    * HTTP
      * added proxyauth support. (#6)
      * let developer allow to rescue a redirect with relative URI. (#28)
      * changed last-chunk condition statement to allow "0000\r\n" marker from
        WebLogic Server 7.0 SP5 instead of "0\r\n". (#30)
      * fixed multipart form submit. (#29, #116)
      * use http_date format as a date in a request header. (#35)
      * avoid duplicated Date header when running under mod_ruby. (#127)
      * reason phrase in Message#reason contains \r. (#122)
      * trim "\n"s in base64 encoded BasicAuth value for interoperability.
        (#149)
      * let retry_connect return a Message not a content. (#119)
      * rescue SocketError and dump a message when a wrong address given. (#152)

    * HTTP-Cookies
      * changed "domain" parameter matching condition statement to allow
        followings; (#24, #32, #118, #147)
        * [host, domain] = [rubyforge.com, .rubyforge.com]
        * [host, domain] = [reddit.com, reddit.com]

    * SSL
      * bundles CA certificates as trust anchors.
      * allow user to get peer_cert. (#117, #123)
      * added wildcard certificate support. (#151)
      * SSL + HTTP keep-alive + long wait causes uncaught exception.  fixed.
        (#120)

    * Connection
      * fixed a loop condition bug that caused intermittent empty response.
        (#150, #26, #125)

  September 16, 2005 - version 2.0.6

    * HTTP
      * allows redirects from a "POST" request.  imported a patch from sveit.
        Thanks! (#7)
      * add 'content-type: application/application/x-www-form-urlencoded' when
        a request contains message-body. (#11)
      * HTTP/0.9 support.  (#15)
      * allows submitting multipart forms.  imported a patch from sveit.
        Thanks! (#7)

    * HTTP-Cookies
      * avoid NameError when a cookie value is nil. (#10)
      * added netscape_rule property to CookieManager (false by default).  You
        can turn on the domain attribute test of Netscape rule with the
        property.  cf. http://wp.netscape.com/newsref/std/cookie_spec.html
      * added HTTPClient#cookie_manager property for accessing its properties.
        (#13)
      * added save_all_cookies method to save unused and discarded cookies as
        well.  The patch is from Christian Lademann.  Thanks!  (#21)
      * allow to set cookie_manager.  raise an error when set_cookie_store
        called and cookie_store has already been set. (#20)

    * SSL
      * allows SSL connection debugging when debug_dev != nil. (#14)
      * skip post_connection_check when
        verify_mode == OpenSSL::SSL::VERIFY_NONE.  Thanks to kdraper. (#12)
      * post_connection_check: support a certificate with a wildcard in the
        hostname. (#18)
      * avoid NameError when no peer_cert and VERIFY_FAIL_IF_NO_PEER_CERT
        given.  Thanks to Christian Lademann.

    * Connection
      * insert a connecting host and port to an exception message when
        connecting failed. (#5)
      * added socket_sync property to HTTPClient(HTTPAccess2::Client) that
        controls socket's sync property.  the default value is true.  CAUTION:
        if your ruby is older than 2005-09-06 and you want to use SSL
        connection, do not set socket_sync = false to avoid a blocking bug of
        openssl/buffering.rb.

  December 24, 2004 - version 2.0.5
    This is a minor bug fix release.
    - Connect/Send/Receive timeout cannot be configured. fixed.
    - IPSocket#addr caused SocketError? on Mac OS X 10.3.6 + ruby-1.8.1 GA.
      fixed.
    - There is a server which does not like 'foo.bar.com:80' style Host header.
      The server for http://rubyforge.org/export/rss_sfnews.php seems to
      dislike HTTP/1.1 Host header "Host: rubyforge.net:80".  It returns
      HTTP 302: Found and redirects to the page again, causes
      HTTPAccess2::Client to raise "retry count exceeded".  Keat found that the
      server likes "Host: rubyforge.net" (not with port number).

  February 11, 2004 - version 2.0.4
    - add Client#redirect_uri_callback interface.
    - refactorings and bug fixes found during negative test.
    - add SSL test.

  December 16, 2003 - version 2.0.3
    - no_proxy was broken in 2.0.2.
    - do not dump 'Host' header under protocol_version == 'HTTP/1.0'

  December ?, 2003 - version 2.0.2
    - do not trust HTTP_PROXY environment variable. set proxy server manually.
      http://ftp.ics.uci.edu/pub/websoft/libwww-perl/archive/2001h1/0072.html
      http://ftp.ics.uci.edu/pub/websoft/libwww-perl/archive/2001h1/0241.html
      http://curl.haxx.se/mail/archive-2001-12/0034.html
    - follow ossl2 change.

  October 4, 2003 - version 2.0.1
    Query was not escaped when query was given as an Array or a Hash.  Fixed.
    Do not use http_proxy defined by ENV['http_proxy'] or ENV['HTTP_PROXY'] if
      the destination host is 'localhost'.
    Hosts which matches ENV['no_proxy'] or ENV['NO_PROXY'] won't be proxyed.
      [,:] separated. ("ruby-lang.org:rubyist.net")
      No regexp. (give "ruby-lang.org", not "*.ruby-lang.org")
      If you want specify hot by IP address, give full address.
	("192.168.1.1, 192.168.1.2")

  September 10, 2003 - version 2.0
    CamelCase to non_camel_case.
    SSL support (requires Ruby/OpenSSL).
    Cookies support.  lib/http-access2/cookie.rb is redistributed file which is
      originally included in Webagent by TAKAHASHI `Maki' Masayoshi.  You can
      download the entire package from http://www.rubycolor.org/arc/.

  January 11, 2003 - version J
    ruby/1.8 support.
