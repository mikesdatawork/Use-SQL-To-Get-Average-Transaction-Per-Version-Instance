![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Use SQL To Get Average Transaction Per Version Instance
**Post Date: July 8, 2015**        


## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's some quick logic to get you the following information about your database server.
1. Server Name
2. SQL Version (complete)
3. Total Combined Database Size (all databases)
4. Number of Days the Service has been online (this is used to determine the average transactions per day. Number of days online gives you the sample)
5. Total transactions across entire sample (days online)
6. Average transactions per day</p>      


## SQL-Logic
```SQL
use master;
 
set nocount on
 
declare @days smallint
 
declare @trans bigint
 
set @days = ( select datediff(d, create_date, getdate()) from sys.databases where database_id = 2 )
 
set @trans = ( select cntr_value from sys.dm_os_performance_counters where counter_name = 'transactions/sec' and instance_name = '_total' )
 
select
 
'server' = @@servername
 
, 'sql version' =
 
cast(substring(left(@@version, 25), 10, 16) as varchar(50)) + ' ' +
 
cast(serverproperty('edition') as varchar(50)) + ' ' +
 
cast(serverproperty('productlevel') as varchar(50)) + ' Build: ' +
 
cast(serverproperty('productversion') as varchar(50))
 
, 'total database size' = cast (convert(decimal(10,2),(sum(size*8.00)/1024.00/1024.00)) as varchar(10)) + ' GB'
 
, 'sample num days' = @days
 
, 'total trans' = @trans
 
, 'average daily trans' = @trans / case when @days = 0 then 1 else @days end
 
from
 
sys.master_files;
```
Note:
The average transactions per day can be calculated with a bit more granularity if necessary. This logic is the total transactions all inserts/updates/deletes etc. and not narrowed down by any means, but it's sufficient to calculate the kind of usage your database instance is experiencing as a whole. For more definitive idea of average transactions per database (which I'll be writing later) should be done per database. This logic is referencing '_total' instance name which is everything under the instance. Thanks goes out to Chris Bell at Waterox for posting his write-up on collecting average transactions. 

More can be found at: https://wateroxconsulting.com/archives/average-transactions-per-day/


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

    
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

