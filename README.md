CSV Match
=========

Find (fuzzy) matches between two CSV files in the terminal.


Installing
----------

    pip install csvmatch

Get an error saying 'command not found'? Sometimes `pip` has a different name -- try typing `pip3` instead. If you get an error saying 'permission denied', try prepending `sudo`.

Usage
-----

Say you have one CSV file such as:

    name,location,codename
    George Smiley,London,Beggerman
    Percy Alleline,London,Tinker
    Roy Bland,London,Soldier
    Toby Esterhase,Vienna,Poorman
    Peter Guillam,Brixton,none
    Bill Haydon,London,Tailor
    Oliver Lacon,London,none
    Jim Prideaux,Slovakia,none
    Connie Sachs,Oxford,none

And another such as:

    Person Name,Location
    Maria Andreyevna Ostrakova,Russia
    Otto Leipzig,Estonia
    George SMILEY,London
    Peter Guillam,Brixton
    Konny Saks,Oxford
    Saul Enderby,London
    Sam Collins,Vietnam
    Tony Esterhase,Vienna
    Claus Kretzschmar,Hamburg

You can then find which names are in both files:

    $ csvmatch data1.csv data2.csv \
        --fields1 name \
        --fields2 'Person Name'

You can also compare multiple columns, so if we wanted to find which name and location combinations are in both files we could:

    $ csvmatch data1.csv data2.csv \
        --fields1 name location \
        --fields2 'Person Name' Location

By default, all columns are used to compare rows. Specific columns can be also be given to be compared -- these should be in the same order for both files. Column headers with a space should be enclosed in quotes. Matches are case-sensitive by default, but case can be ignored with `-i`.

Other things can also be ignored. We can ignore non-alphanumeric characters (`-a`), characters from non-latin alphabets (`-n`), the order words are given in (`-s`), and the order letters are given in (`-e`), and common English name prefixes such as Mr and Ms (`-t`). Terms specific to your data can be ignored by passing a text file containing a regular expression on each line (`-l`).

By default the columns used in the output are the same ones used for matching. Other sets of columns can be specified using the `--output` parameter. This takes a space-separated list of column names, each prefixed with a number and a dot indicating which file that field is from:

    $ csvmatch data1.csv data2.csv \
        --fields1 name location \
        --fields2 'Person Name' Location \
        --output 1.name '2.Person Name' 2.Location \
        > results.csv

There are also some special column definitions. `1*` and `2*` expand into all columns from that file. Where a fuzzy matching algorithm has been used `degree` will add a column with a number between 0 - 1 indicating the strength of each match.

By default the two files are linked using an inner join -- only successful matches are returned. However using `-j` you can specify a `left-outer` join which will return everything from the first file, whether there was a match or not. You can also specify `right-outer` to do the same but for the second file, and `full-outer` to return everything from both files.

We can combine some of the above options to perform operations alike Excel's `VLOOKUP`. So if we wanted to add a column to `data2.csv` giving the codename of each person that is specified in `data1.csv`:

    $ csvmatch data1.csv data2.csv \
        --fields1 name \
        --fields2 'Person Name' \
        --join right-outer \
        --output 2* 1.codename \
        > results.csv


