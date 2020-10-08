[![build status](https://secure.travis-ci.org/dankogai/p5-tie-array-lazy.png)](http://travis-ci.org/dankogai/p5-tie-array-lazy)
# NAME

Tie::Array::Lazy - Lazy -- but mutable -- arrays.

# VERSION

$Id: Lazy.pm,v 0.2 2012/08/09 19:13:00 dankogai Exp dankogai $

# SYNOPSIS

    use Tie::Array::Lazy;
    # 0..Inf
    tie my @a, 'Tie::Array::Lazy', [], sub{ $_[0]->index };
    print @a[0..9];      # 0123456789
    $a[1] = 'one';
    print @a[0..9];      # 0one23456789
    print "$_\n" for @a; # prints forever

# DESCRIPTION

[Tie::Array::Lazy](https://metacpan.org/pod/Tie%3A%3AArray%3A%3ALazy) implements a _lazy array_, an array that
generates the element on demand.  It is a lot like a _lazy list_ but
unlike lazy lists seen in many functional languages like Haskell, lazy
arrays are mutable so you can assign values to their elements.

The example below explains how it works.

    tie my @a, 'Tie::Array::Lazy', [3,2,1,0], sub{ 1 };
    my @r = splice @a, 1, 2, qw/two one/
    # @r is (2,1);     tied(@a)->array is [3,'two','one',0];
    pop @a;   # 0;     tied(@a)->array is [3,'two','one'];
    shift @a; # 3;     tied(@a)->array is ['two','one'];
    shift @a; # 'two'; tied(@a)->array is ['one'];
    pop @a;   # 'one;  tied(@a)->array is [];
    pop @a;   # 1;     tied(@a)->array is [];
    @a[3] = 3 #        tied(@a)->array is [1,1,1,3];

You can think _lazy arrays_ as arrays that auto-fills.

# EXPORT

None.

# FUNCTIONS

## tie @array, 'Tie::Array::Lazy', \*arrayref\*, \*coderef\*

makes @array a lazy array with its initial state with _arrayref_ and
element generator code with _coderef_. Here is an exmaple;

The _coderef_ is a code reference which `$_[0]` is the
Tie::Array::Lazy object itself (tied(@array)) and `$_[1]` is the
index.

In addition to methods that [Tie::Array](https://metacpan.org/pod/Tie%3A%3AArray) provides, the object has
methods below:

- array

    The reference to the internal array which stores values either
    generated or assigned.

        # Fibonacci array
        tie my @a, 'Tie::Array::Lazy', 
            [0, 1],
            sub{ $_[0]->array->[-2] + $_[0]->array->[-1] }

- index

    Shorthand for `scalar @{ $self->array }`.

        # 0..Inf
        tie my @a, 'Tie::Array::Lazy', [], sub{ $_[0]->index };

- maker

    The reference to the code reference to generate the value needed.
    Whenever the value is needed, [Tie::Array::Lazy](https://metacpan.org/pod/Tie%3A%3AArray%3A%3ALazy) invokes the code
    below;

        $self->maker($self, $index);

## Using [Tie::Array::Lazy](https://metacpan.org/pod/Tie%3A%3AArray%3A%3ALazy) as a base class

Like [Tie::Array](https://metacpan.org/pod/Tie%3A%3AArray), you can use this module as a base class.  See
[Tie::Array::Lazier](https://metacpan.org/pod/Tie%3A%3AArray%3A%3ALazier) for details.

# AUTHOR

Dan Kogai, `<dankogai at cpan.org>`

# BUGS

Please report any bugs or feature requests to
`bug-tie-array-lazy at rt.cpan.org`, or through the web interface at
[http://rt.cpan.org/NoAuth/ReportBug.html?Queue=Tie-Array-Lazy](http://rt.cpan.org/NoAuth/ReportBug.html?Queue=Tie-Array-Lazy).
I will be notified, and then you'll automatically be notified of progress on
your bug as I make changes.

# SUPPORT

You can find documentation for this module with the perldoc command.

    perldoc Tie::Array::Lazy

You can also look for information at:

- AnnoCPAN: Annotated CPAN documentation

    [http://annocpan.org/dist/Tie-Array-Lazy](http://annocpan.org/dist/Tie-Array-Lazy)

- CPAN Ratings

    [http://cpanratings.perl.org/d/Tie-Array-Lazy](http://cpanratings.perl.org/d/Tie-Array-Lazy)

- RT: CPAN's request tracker

    [http://rt.cpan.org/NoAuth/Bugs.html?Dist=Tie-Array-Lazy](http://rt.cpan.org/NoAuth/Bugs.html?Dist=Tie-Array-Lazy)

- Search CPAN

    [http://search.cpan.org/dist/Tie-Array-Lazy](http://search.cpan.org/dist/Tie-Array-Lazy)

# ACKNOWLEDGEMENTS

Nick Ing-Simmons for [Tie::Array](https://metacpan.org/pod/Tie%3A%3AArray)

Matsumoto Yukihiro (Matz) for teasing me into hacking this module.

# COPYRIGHT & LICENSE

Copyright 2007-2020 Dan Kogai, all rights reserved.

This program is free software; you can redistribute it and/or modify it
under the same terms as Perl itself.
