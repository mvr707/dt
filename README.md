# dt
Data Tracking/Transormation Framework


in => [dt] => out

in = a sequence of Tables
out = a sequence of Tables

Table = 2-dimensional data - either supplied directly (e.g. csv), or indirectly via structured (e.g. file, SQL query) or unstructured (HTML table from a webpage) sources

Table may be specified as  a header + sequence of Rows, or Columns

        E.g.
                Name, Age, Sex
                aaa, 11, M
                bbb, 22, F
                ccc, 33, M

or as sequence of records

        E.g.
                { Name => aaa, Age => 11, Sex => M }
                { Name => bbb, Age => 22, Sex => F }
                { Name => ccc, Age => 33, Sex => M }

Multiple Tables can be on same stream (or file) separated by an empty line, or in multiple sheets of a spreadsheet.

in =  [ { file => 'name' or content => 'string' or CODEREF, format => csv/tsv/psv/xml/json/ods/xls/xlsx/... }, ... ]
out = [ { file => 'name', format => ... } ]

[dt] has value added features:
        provides services (e.g. join, merge, pivot, group, average etc) to manipulate in-stream, to generate out-stream.
        services can be grouped to form "transoformations", which can be composed.
        result streams can be persisted into a DB and revision controlled (e.g. one can see how a table of data evolved over time)
        
        
Goal: Ability to compose tabular data from multiple sources in an automation friendly framework, AND track the results in a revision controlled fashion.

Sources can be structured (e.g. Relational DB, xls/csv/tsv/ods etc formats) or unstructured (e.g. HTML table in a webpage).
Automation can schedule the jobs (say) via a cron job, or Interactively view results.
Ability to combine (e.g. join, pivot, group, order, subset, union, intersection etc) is essential.
Persistence to results (final as well as intermediate)
Tracking to find trend over time. E.g. How a row (or a field of a table) evolved over time.

Imagine a use case...Combine diverse data (from MS Excel on Dropbox, Numbers worksheet on Mac, and a HTML Table in a webpage) using web services (e.g. find stock price) and put the result into a SQLite Database and push the result to a web page and a Google Sheet for users to view. Same can be seen from a terminal in interactive fashion - with periodic refresh.

Wow...that seems like a valuable weapon that saves tons of cycles, at least in my world. :-)

Reviewed the version 0.01 of the tool. The tool was built using easydatabase (https://sites.google.com/site/easydatabase/) and sqlite3, using Perl's ability glue diverse data sources together.

[snip]
$ dt infile=test.csv [outformat=psv]
              Formats input to unform width 'psv' (pipe seperated values)
$ cat test.csv | dt
              Takes input from a pipe
$ dt infile=test.csv infile=test1.csv command='$dt->[0]=$dt->[0]->join($dt->[1], 0, ["Name"], ["Name"], {renameCol => 1})' outformat=xls outfile=t.xls
               Composes tables and stores results
$ cat t.csv | dt informat=csv command='dt2db($dt->[0], undef, "t.db", "t")'
                Creates persistent DB tables from in-memory Data::Table objects
$ sqlite3 -header t.db "select * from t"
                DB and SQL access
$ cat t.csv | dt informat=csv command='dt2db($dt->[0], undef, "t.db", "t")'
                 When data changes, only changed rows are updated giving a time history of data
[/snip]

Question: Is there a tool out there that solves this class of problems? If so, we can learn and adopt; else we can refine and release the tool.
