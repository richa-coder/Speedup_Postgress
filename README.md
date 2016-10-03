# Speedup_Postgress
Things get worst  when we  increase the number of keys in DATABASE making someone wait in front of their browser for the graph editor to respond.
Ex:
Buffers: shared hit=83494
  ->  Bitmap Heap Scan on context c  (cost=5667..11762.31 rows=1 width=329) (actual time=17128.121..22031.783 rows=10858 loops=1)
        Recheck Cond: ((tags @> '{hey}'::text[]) AND (x_key = 1))
        Filter: (key = ANY ('{16968196,(a lot more keys here)}'::integer[]))


Postgress uses Bitmap Heap Scan.In our case it has a large number of keys to check so it uses the more approximative way to retrieve the candidate rows and checks each row individually for a match on x_key and tags. All this “loading in memory” and “checking individual row” takes time.
 
 Solution:The query optimizer is fooled by the use of ARRAY[...] to list all the primary keys to look up. VALUES (...), (...) lets the optimizer make full use of the primary key index instead.
 Ref:Lane
