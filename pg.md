```
-- pg_waldump -p ./pg_wal -z 0000000100000062000000B9

CREATE EXTENSION pageinspect;

create or replace function infomask2_tostr(infomask integer, out result text) as $$
begin
    result := '';
    -- ./src/include/access/htup_details.h
    if (infomask & x'2000'::integer) != 0 then
        -- tuple was updated and key cols modified, or tuple deleted
        result = result || 'HEAP_KEYS_UPDATED';
    end if;
    if (infomask & x'4000'::integer) != 0 then
        result = result || '|HEAP_HOT_UPDATED';
    end if;
    if (infomask & x'8000'::integer) != 0 then
        result = result || '|HEAP_ONLY_TUPLE';
    end if;
    if(starts_with(result, '|')) then
        result = substr(result, 2);
    end if;
end;
$$ language plpgsql;

create or replace function lpflags_tostr(lp_flags integer) returns text as $$
    select case when lp_flags = 0 then 'LP_UNUSED'
       when lp_flags = 1 then 'LP_NORMAL'
       when lp_flags = 2 then 'LP_REDIRECT'
       when lp_flags = 3 then 'LP_DEAD'
    end;$$ language sql;

select ctid, b.* from elsci.dev_peaksel.batches b where id ='86STVRBfAjO';

SELECT * FROM page_header(get_raw_page('elsci.dev_peaksel.batches', 110));
select lp, lp_off, lpflags_tostr(lp_flags) as lp_flags, lp_len,
       t_xmin, t_xmax, t_ctid,
       t_infomask, infomask2_tostr(t_infomask2)
from heap_page_items(get_raw_page('elsci.dev_peaksel.batches', 110));

select * from bt_metap('elsci.dev_peaksel.batches_pkey');
select * from bt_page_stats('elsci.dev_peaksel.batches_pkey', 1);
select * from bt_page_items('elsci.dev_peaksel.batches_pkey', 1);


select * from bt_page_items(get_raw_page('bt_page_items', 1));
```
