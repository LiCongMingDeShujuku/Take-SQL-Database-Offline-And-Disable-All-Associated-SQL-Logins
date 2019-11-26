![CLEVER DATA GIT REPO](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/0-clever-data-github.png "李聪明的数据库")

# 使数据库脱机并禁用所有关联登录
#### Take Database Offline And Disable All Associated Logins
**发布-日期: 2016年02月16日 (评论)**

## Contents

- [中文](#中文)
- [English](#English)
- [SQL Logic](#Logic)
- [Build Info](#Build-Info)
- [Author](#Author)
- [License](#License) 


## 中文
以下SQL逻辑使数据库脱机，然后禁用具有与数据库名称关联的数据库的所有后续登录。在这种情况下，我们使用外卡。在这个例子中，我们使用的是旧的TRACKIT数据库。在你退出旧数据库环境但不一定要删除任何数据库或其用户的情况下，此逻辑非常有用。传统的方法是简单地使数据库脱机，并禁用它的用户。然后，在30天后，你可以通过删除数据库和用户来停用数据库。以下已经包含了几个快速查询以确认数据库已脱机，并且已禁用登录，包括修改登录时的时间戳。


## English
The following SQL logic takes a database offline, and then disables all subsequent logins of the database that have a name that is associated with the database name. In this case we are using a wild card. In this example we are using an old TRACKIT database. This logic is useful in cases where you are decommissioning old database environments, but don’t necessarily want to drop any Databases or their users. A traditional approach is to simply take the database offline, and disable it’s users. Then after a period of 30 days you can decommission the database by removing it and the users. I’ve included a couple quick queries to confirm the database has been taken offline, and the logins have been disabled including a timestamp when the logins were modified.

---
## Logic
```SQL
-- take the database offline
-- 使数据库脱机
alter database [TRACKIT8] set offline
with rollback immediate
 
-- disable all logins associated with same database name using wildcard
-- 使用通配符禁用与相同数据库名称关联的所有登录
declare @disable_trackit_logins varchar(max)
set     @disable_trackit_logins = ''
select  @disable_trackit_logins = @disable_trackit_logins + 
        'alter login [' + name + '] disable;' + char(10)
from    syslogins where name like 'track%' order by name asc
exec    (@disable_trackit_logins) --for xml path(''), type
 
-- confirm database is offline
-- 确认数据库已脱机
select
    'database'          = upper(name)
,   'is_offline'        = state_desc
from
    sys.databases
where
    name like 'trackit%'
 
-- confirm all logins have been disabled
-- 确认已禁用所有登录
select
    'login'             = upper(name)
,   'is_disabled'       =
                        case is_disabled
                            when 0 then 'Enabled'
                            when 1 then 'Disabled'
                        end
,   'modified_on'       = left(modify_date, 19)
from
    sys.sql_logins
where
    name like 'Trackit%'


```

![#](images/take-sql-databases-offline-and-disable-all-asscoiated-sql-logins.png?raw=true "#")

[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

- **李聪明的数据库 Lee's Clever Data**
- **Mike的数据库宝典 Mikes Database Collection**
- **李聪明的数据库** "Lee Songming"

[![Gist](https://img.shields.io/badge/Gist-李聪明的数据库-<COLOR>.svg)](https://gist.github.com/congmingshuju)
[![Twitter](https://img.shields.io/badge/Twitter-mike的数据库宝典-<COLOR>.svg)](https://twitter.com/mikesdatawork?lang=en)
[![Wordpress](https://img.shields.io/badge/Wordpress-mike的数据库宝典-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

---
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Lee Songming](https://raw.githubusercontent.com/LiCongMingDeShujuku/git-resources/master/1-clever-data-github.png "李聪明的数据库")

