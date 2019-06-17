# jmx_exporter_hikari

I found that the official documentation was missing few informations to be able to create my own config files.

First I recommend you to scrap all the raw data with this config:

lowercaseOutputLabelNames: true

lowercaseOutputName: true
rules:

\- pattern: (.*)

Here is the raw data that I collected from hikaricp:

\# HELP com_zaxxer_hikari_pool_some_string_activeconnections ActiveConnections (com.zaxxer.hikari<type=Pool (some_string)><>ActiveConnections)

\# TYPE com_zaxxer_hikari_pool_some_string_activeconnections untyped
com_zaxxer_hikari_pool_sillage_wa_bureautique_datasourcesidosmed_activeconnections 0.0

Then you need to create a regex expression that will catch the information you need.

Example : 

\#  name: com.zaxxer.hikari_$1_$2
\- pattern: 'com.zaxxer.hikari<type=Pool(.*)>(ActiveConnections|IdleConnections|ThreadsAwaitingConnection|TotalConnections):'
  name: document_hikari_$1_$2
  
pattern corresponds to a regex expression. (for more details check the official documentation : https://github.com/prometheus/jmx_exporter)
By following the rules you have provided, it will try to match a string in the raw data that you have collected previously.

Check this website to verify that your pattern catches the raw data : https://regex101.com/

$1, $2 are some strings collected buy a regex expression in your pattern.
You can have $1, $2, $3, ... $N collected strings.
Those strings are created by any pattern that consumes more than one character.

ex:
- (\w+)
- (.*)
- (ActiveConnections|IdleConnections)

Example : 
'com.zaxxer.hikari<type=PoolConfig (some_string)><>MaximumPoolSize)'

So with our config and the previous line of data, the values of $1 $2 will be : 

- $1 : some_string

- $2 : MaximumPoolSize
