# Test::Archive::Libarchive ![linux](https://github.com/uperl/Test-Archive-Libarchive/workflows/linux/badge.svg)

Testing tools for Archive::Libarchive

# SYNOPSIS

```perl
use Test2::V0;
use Archive::Libarchive;
use Test::Archive::Libarchive;
use Path::Tiny qw( path );

my $w = Archive::Libarchive->new;
la_ok $w, 'add_filter_gzip';
la_ok $w, 'set_format_pax_restricted';
la_ok $w, 'open_filename' => ['foo.tar.gz'];

my $e = Archive::Libarchive::Entry->new;
$e->set_pathname(__FILE__);
$e->set_size(-s __FILE__);
$e->set_filetype('reg');

la_ok $w, 'write_header' => [$e];
is($w->write_data(path(__FILE__)->slurp_raw), -s __FILE__);

la_ok $w, 'close';

done_testing;
```

# DESCRIPTION

Error handling for `libarchive` and [Archive::Libarchive](https://metacpan.org/pod/Archive::Libarchive) is fairly
primitive.  Most methods return an `int` which correspond to
`ARCHIVE_EOF`, `ARCHIVE_OK`, `ARCHIVE_RETRY`, `ARCHIVE_WARN`,
`ARCHIVE_FAILED` or `ARCHIVE_FATAL`.  Some methods will also
return the number of actual bytes written on success and one of
these codes on failure.  It can be tedious doing the necessary
checks for each method call in a test, so this module provides tools
for testing [Archive::Libarchive](https://metacpan.org/pod/Archive::Libarchive) method calls that follow this
pattern.

# FUNCTIONS

## la\_ok

```
la_ok $a, $method;
la_ok $a, $method, \@arguments;
la_ok $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_OK.
This indicates a successful method call.

## la\_eof

```
la_eof $a, $method;
la_eof $a, $method, \@arguments;
la_eof $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_EOF.
This is for when you reach the end of the archive or a file.

## la\_warn

```
la_warn $a, $method;
la_warn $a, $method, \@arguments;
la_warn $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_WARN.
This indicates a possible problem, but things are still working.

## la\_retry

```
la_retry $a, $method;
la_retry $a, $method, \@arguments;
la_retry $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_RETRY.
This indicates that a retry might succeed.

## la\_failed

```
la_failed $a, $method;
la_failed $a, $method, \@arguments;
la_failed $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_RETRY.
Operation failed.

## la\_fatal

```
la_failed $a, $method;
la_failed $a, $method, \@arguments;
la_failed $a, $method, \@arguments, $test_name;
```

Tests that calling the method `$method` on the archive object `$a` returns ARCHIVE\_RETRY.
Operation failed so badly that the archive object is no longer usable.

## la\_read\_data\_ok

```perl
my $content = la_read_data_ok $a;
my $content = la_read_data_ok $a, $test_name;
```

Tests that the data can be read from the archive.  The entire content section will be read
and returned on success.  If there is a failure during the read then the test will fail.

# CAVEATS

Not all methods in [Archive::Libarchive](https://metacpan.org/pod/Archive::Libarchive) return a result code like this.  Some
like `write_data` return the number of bytes on success.

# SEE ALSO

- [Archive::Libarchive](https://metacpan.org/pod/Archive::Libarchive)

# AUTHOR

Graham Ollis <plicease@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2021 by Graham Ollis.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
