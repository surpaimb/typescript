
TypeScript 已经成为一个强大的 Web 应用程序开发环境，在与标准 JavaScript 保持一致的同时，提供了显著的改进。在本文中，我们将深入探讨使用 TypeScript 相关的细节，创建一个强大的解决方案来管理服务器端 TypeScript 的数据库访问。

要点：

TypeScript 是 JavaScript 的超集，具有严格的语法和类型。
TypeScript 打破了 JavaScript 在现代大型应用程序中的应用限制。
Decorator 是一种实验性的特性，用于注解类声明、方法、访问器、属性或其他注解。
TypeScript 在 TIOBE 编程语言索引中排名前 50。
TypeORM 包提供了对象到关系的映射来访问支持 TypeScript 的关系数据库，比如 MySQL 数据库。
JavaScript 是一门基于ECMAScript 规范的脚本语言。JavaScript 已经从客户端脚本语言发展成为同时可在客户端和服务器端运行的脚本语言。服务器端 JavaScript 最引人注目的实现是 Node.js。

问题
JavaScript 缺乏大型现代 Web 应用程序可使用的特性，比如类型注释、编译时类型检查和类型推断，等等。JavaScript 代码在大型应用程序中变得非常复杂。

解决方案
TypeScript 是 JavaScript 的类型化超集，旨在打破 JavaScript 在大型应用程序中的一些限制。

TypeScript 是严格的 JavaScript 语法超集，加入了诸如编译时类型检查、类型注释、类型推断、类型擦除等特性，还支持接口和面向对象特性。TypeScript 是一门开源的脚本语言，可以被转换为 JavaScript。转换后的输出就是常见的 JS，而不是只有机器才能读取的东西。

TypeScript 加入了一个叫作 Decorator 的实验性特性，可以使用注释和元编程语法给类和类成员添加额外的特性。Decorator 采用了 @expression 的声明方式，其中 expression 作为在运行时被调用的函数，并带有相关的声明信息。Decorator 可用于注解类的声明、方法、访问器、属性或其他注解。本文中将会使用 Decorator。

2012 年推出的 TypeScript 最近大受欢迎。最近发布的一份有关 JavaScript 和 Web 开发的InfoQ 趋势报告指出，“TypeScript 的受欢迎程度急剧上升，目前已进入 GitHub 最受欢迎前 10 种编程语言之列……”2018 年 6 月，TypeScript 首次出现在 TIOBE 编程语言排名前 100 的榜单上，排在第 93 位。最近，TypeScript 在 TIOBE 索引中排名第 44 位。

TypeScript 已经成为开发 Web 应用程序的一个强大的环境，在与标准 JavaScript 保持一致的同时，提供了其他显著的改进。在本文中，我们将深入探讨使用 TypeScript、Node.js 和 TypeORM 创建一个强大的解决方案来管理服务器端的数据库访问。我们将构建一个示例 CRUD 应用程序，作为一个完整的端到端解决方案。我们假定读者都很熟悉 JavaScript。本文将包含以下几个小节。

搭建环境
创建项目
配置项目
创建实体
创建数据库连接，生成数据表
运行应用程序
查看数据表
使用 Repository 添加数据
使用连接管理器查找实体
使用 Repository 查找实体
更新数据
移除数据
创建一对一的关系
通过关系查找对象
创建一对多的关系
创建多对多的关系
搭建环境
下载和安装相关软件。

Node.js
MySQL 数据库
接下来，我们需要安装一些 Node.js 模块（包）。我们需要使用TypeORM包，这个包为 TypeScript 提供了对象到关系的映射，用来访问包括 MySQL 在内的大多数关系型数据库。

安装 typeorm 包。

复制代码
npm install typeorm -g
安装 reflect-metadata 库，因为在使用类 Decorator 时需要使用它。跟 Decorator 一样，reflect-metadata 库也是实验性的。

复制代码
npm install reflect-metadata -g
安装 Node 类型。

复制代码
npm install @types/node -g
安装 MySQL 数据库驱动程序。

复制代码
npm install mysql -g
创建项目
创建一个用于 MySQL 数据库的 TypeORM 项目，项目名称随意（比如 MySQLProject）。

复制代码
typeorm init --name MySQLProject --database mysql
项目创建好后会生成一个 MySQLProject 目录，进入这个目录，可以列出项目文件。

复制代码
cd MySQLProject
C:\Typescript\MySQLProject>DIR
 Volume in drive C is OS
 Volume Serial Number is BEFC-C04A
 Directory of C:\Typescript\MySQLProject
02/02/2019  05:17 PM  <DIR>        .
02/02/2019  05:17 PM  <DIR>       ..
02/02/2019  05:13 PM              47 .gitignore
02/02/2019  05:17 PM  <DIR>       node_modules
02/02/2019  05:13 PM            473 ormconfig.json
02/02/2019  05:17 PM          46,178 package-lock.json
02/02/2019  05:17 PM            406 package.json
02/02/2019  05:13 PM            172 README.md
02/02/2019  05:13 PM  <DIR>       src
02/02/2019  05:13 PM            298 tsconfig.json
            6 File(s)       47,574 bytes
            4 Dir(s)  25,897,005,056 bytes free
C:\Typescript\MySQLProject>
安装项目依赖项。

复制代码
npm install
命令输出如下所示：

复制代码
C:\Typescript>typeorm init --name MySQLProject --database mysql
Project created inside C:\Typescript/MySQLProject directory.
C:\Typescript>cd MySQLProject
 
C:\Typescript\MySQLProject>npm install
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN MySQLProject@0.0.1 No repository field.
npm WARN MySQLProject@0.0.1 No license field.
added 153 packages from 447 contributors and audited 231 packages in 70.022s
found 0 vulnerabilities
MySQLProject 的配置和源码可以从GitHub上下载，其中还包括本文将要添加的脚本文件。

配置项目
TypeScript 的编译器选项配置在tsconfig.json中。修改 tsconfig.json，启用下面的特性：

复制代码
"emitDecoratorMetadata": true,
"experimentalDecorators": true,
其他的编译器选项，比如目标编译版本，已经在 tsconfig.json 预先配置好了。–experimentalDecorators 选项用于启用 Decorator 特性。–emitDecoratorMetadata 选项用于为被装饰的声明触发类型元数据。这里需要导入 reflect-metadata 库，用来触发装饰器元数据。

修改ormconfig.json的数据库配置，以便连接到 MySQL 数据库。如果使用的是本地数据库，就使用默认的 host 和 port，username 和 password 填写实际的用户名和密码即可。

复制代码
{
   "type": "mysql",
   "host": "localhost",
   "port": 3306,
   "username": "root",
   "password": "mysql",
   "database": "mysql",
   "synchronize": true,
   "logging": false,
   "entities": [
      "src/entity/**/*.ts"
   ],
   "migrations": [
      "src/migration/**/*.ts"
   ],
   "subscribers": [
    "src/subscriber/**/*.ts"
   ]
}
创建实体
在这一小节，我们将创建一个实体，作为日志目录应用程序的模型。一个实体就是一个类，映射到一个数据表。使用 typeorm 库提供的 @Entity() 就可以将一个类声明为一个实体。在 entity 目录中添加一个 Catalog.ts 文件来定义一个实体。在这个文件中使用 import 语句来导入 typeorm 库提供的 Entity、Column 和 PrimaryGenerationColumn 函数。

复制代码
import {Entity, Column, PrimaryGeneratedColumn} from "typeorm";
导出一个叫作 Catalog 的类，并使用 @Entity() 注解它。

复制代码
@Entity()
export class Catalog {
…
}
完整的Catalog.ts文件可在 GitHub 上查看。一个基本的实体由多个列组成，并且每个实体必须指定至少一个主列。TypeORM 提供了几种主列，如表 1 所示。

主列类型	描述
@PrimaryColumn()	创建一个主列。列的类型是可选的，如果没有指定，则从属性类型进行推断。主列的值必须由用户提供。
@PrimaryGeneratedColumn()	创建一个主列，类型为 int，值是自增的。
@PrimaryGeneratedColumn(“uuid”)	创建一个主列，值为自动生成的唯一 uuid。
表 1：主列类型
在 Catalog 实体中增加一个叫作 id 的主列。MySQL 数据库支持自增的主列，会为每个新数据行自动分配一个唯一的主键值。要启用这个特性，请使用 @PrimaryGeneratedColumn。

复制代码
@PrimaryGeneratedColumn()
  id: number;
然后加入其他列：journal、publisher、edition、title 和 author，都是 string 类型。再增加一个叫作 isPublished 的列，类型为 Boolean，用来表示条目是否已发布。实体的属性类型被适当地映射成数据库列的类型，具体因不同的数据库而异。string 类型会被映射成 varchar(255)，或者其他类似的数据库类型。number 类型会被映射成 integer，或者其他类似的数据库类型。实体到数据库的映射也可以由用户指定。例如，将 string 类型的 title 列映射到 MySQL 数据库的 text 类型。

复制代码
@Column("text")
  title: string;
string 类型的默认长度是 255，但也可以指定自定义长度：

复制代码
@Column({
      length: 25
  })
  edition: string;
将下列代码拷贝到实体文件中。

复制代码
import {Entity, Column, PrimaryGeneratedColumn} from "typeorm";
@Entity()
export class Catalog {
    @PrimaryGeneratedColumn()
  id: number;
  @Column()
  journal: string;
 
  @Column()
  publisher: string;
 
  @Column({
      length: 25
  })
  edition: string;
 
    @Column("text")
  title: string;
 
  @Column()
  author: string;
 
  @Column()
  isPublished: boolean;
}
创建数据库连接，生成数据表
因为我们要开发的是一个 CRUD 的应用程序，所以需要连接到 MySQL 数据库。

在这个小节，我们需要编写一个 TypeScript 脚本来连接 MySQL 数据库，并为 entity 目录中的实体创建数据表。我们在 entity/Catalog.ts 定义了一个实体，请把这个目录中的其他文件删除，包括项目默认生成的 User.ts。在 MySQLProject 项目的 src 目录中创建一个 index.ts 文件，并导入 reflect-metadata 库。从 typeorm 库中导入 createConnection 函数，然后导入 entity 目录中的 Catalog 类。在这里我们应该使用 ES2017 提供的 async/await 语法。await 关键字将会挂起脚本的执行，直到 promise 得到满足。使用 createConnection 函数创建连接，连接选项包括 type、host、port、username、password、database 和 entities。

复制代码
createConnection({
  type: "mysql",
  host: "localhost",
  port: 3306,
  username: "root",
  password: "mysql",
  database: "mysql",
  entities: [
     __dirname + "/entity/*.ts"
  ],
  synchronize: true,
  logging: false
}).then(async connection => {
…
…
}).catch(error => console.log(error));
在 then 代码块中创建一个 Catalog 实体。

复制代码
 let catalog = new Catalog();
设置实体的属性。

复制代码
  catalog.journal = "Oracle Magazine";
  catalog.publisher = "Oracle Publishing";
  catalog.edition = "March-April 2005";
  catalog.title = "Starting with Oracle ADF";
  catalog.author = "Steve Muench";
  catalog.isPublished = true;
获取 EntityManager 实例，调用 save 方法来保存实体。

复制代码
await connection.manager.save(catalog);
save 方法将所有给定的实体保存到数据库中。这个方法首先会验证实体是否已经存在数据库中，如果存在，它将会更新这个实体，如果不存在，就将实体保存到数据库中。用类似的方法添加另外一个实体。src/index.ts 的代码如下：

复制代码
import "reflect-metadata";
import {createConnection} from "typeorm";
import {Catalog} from "./entity/Catalog";
 
createConnection({
  type: "mysql",
  host: "localhost",
  port: 3306,
  username: "root",
  password: "mysql",
  database: "mysql",
  entities: [
      __dirname + "/entity/*.ts"
  ],
  synchronize: true,
  logging: false
}).then(async connection => {
 
  let catalog = new Catalog();
  catalog.journal = "Oracle Magazine";
  catalog.publisher = "Oracle Publishing";
  catalog.edition = "March-April 2005";
  catalog.title = "Starting with Oracle ADF";
  catalog.author = "Steve Muench";
  catalog.isPublished = true;
 
  await connection.manager.save(catalog);
  console.log('Catalog has been saved'+'\n');
 
     let catalog2 = new Catalog();
  catalog2.journal = "Oracle Magazine";
  catalog2.publisher = "Oracle Publishing";
  catalog2.edition = "November December 2013";
  catalog2.title = "Engineering as a Service";
  catalog2.author = "David A. Kelly";
  catalog2.isPublished = true;
 
  await connection.manager.save(catalog2);
  console.log('Catalog has been saved'+'\n');
 
}).catch(error => console.log(error));
运行应用程序
使用下面的命令运行应用程序。

复制代码
npm start
在运行 index.ts 时，它会连接到数据库，并在数据库中创建指定的实体。数据库中只会有一个 Catalog 表。这个命令的输出如下所示：

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
Catalog has been saved
Catalog has been saved
查看数据库表
接下来，我们将通过 MySQL CLI 来查看生成的数据库表。启动 MySQL CLI，使用 mysql 连接数据库。

复制代码
C:\mysql-5.7.25-winx64\mysql-5.7.25-winx64\bin>mysql -u root -p
Enter password: *****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 7
Server version: 5.7.25 MySQL Community Server (GPL)
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql>  
切换到 mysql 数据库。

复制代码
mysql> use mysql
Database changed
列出数据表，之前生成的 catalog 表也在其中。

复制代码
mysql> SHOW TABLES;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| catalog                   |
…
| user                      |
+---------------------------+
32 rows in set (0.00 sec)
 
Describe the catalog table and it lis
使用 desc 命令，它将列出与实体 Catalog 字段对应的列。因为我们使用了自动生成的自增长主键列，数据库自动为我们设置 id 列的值。

复制代码
mysql> DESC catalog;
+-------------+--------------+------+-----+---------+----------------+
| Field     | Type      | Null | Key | Default | Extra        |
+-------------+--------------+------+-----+---------+----------------+
| id        | int(11)   | NO   | PRI | NULL | auto_increment |
| journal   | varchar(255) | NO   |   | NULL    |             |
| publisher   | varchar(255) | NO   |   | NULL    |             |
| edition   | varchar(25)  | NO   |     | NULL  |             |
| title     | text      | NO   |  | NULL  |             |
| author    | varchar(255) | NO   |   | NULL    |             |
| isPublished | tinyint(4)   | NO   |   | NULL    |             |
+-------------+--------------+------+-----+---------+----------------+
7 rows in set (0.01 sec)
运行 SQL 的 SELECT 语句获取数据。

复制代码
mysql> SELECT * FROM catalog;
| id | journal      | publisher       | edition             | title
            | author      | isPublished |
|  1 | Oracle Magazine | Oracle Publishing | March-April 2005     | Starting w
ith Oracle ADF | Steve Muench   |         1 |
|  2 | Oracle Magazine | Oracle Publishing | November December 2013 | Engineering as a Service | David A. Kelly |         1 |
 
2 rows in set (0.01 sec)
 
mysql>
使用 Repository 添加数据
我们可以通过两种方式来操作实体，一个是 EntityManager，我们已经用过了，还有一种是 Repository，我们将在这一小节中使用它。如果项目中有很多实体，使用 Repository 更合适，因为每种实体可以有自己的 Repository。Repository 提供了与 EntityManager 相似的功能。我们已经通过 EntityManager 往数据库中添加了两个实体。在这一小节中，我们将使用 Repository 添加第三个实体。我们将继续使用 index.ts，所以需要将之前使用 EntityManager 创建 Catalog 条目的代码移除。然后，像之前那样创建 Catalog 实体，并从数据库中连接中获取 Repository 实例。

复制代码
let catalogRepository = connection.getRepository(Catalog);
使用 save 函数保存 Catalog 实例。

复制代码
await catalogRepository.save(catalog);
index.ts 的代码如下：

复制代码
import {createConnection} from "typeorm";
import {Catalog} from "./entity/Catalog";
createConnection({
  type: "mysql",
  host: "localhost",
  port: 3306,
  username: "root",
  password: "mysql",
  database: "mysql",
  entities: [
      __dirname + "/entity/*.ts"
  ],
  synchronize: true,
  logging: false
}).then(async connection => {
 
let catalog = new Catalog();
  catalog.journal = "Oracle Magazine";
  catalog.publisher = "Oracle Publishing";
  catalog.edition = "November December 2013";
  catalog.title = "Quintessential and Collaborative";
  catalog.author = "Tom Haunert";
  catalog.isPublished = true;
 
  let catalogRepository = connection.getRepository(Catalog);
 
  await catalogRepository.save(catalog);
  console.log('Catalog has been saved'+'\n');
 
  let [all_Catalogs, CatalogsCount] = await catalogRepository.findAndCount();
 
  console.log('Catalogs count: ', CatalogsCount+'\n');
 
}).catch(error => console.log(error));
像之前那样运行应用程序，数据库中就会出现第三个 Catalog 实体。因为之前已经添加过两个实体，所以现在数据库中有三个实体。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
Catalog has been saved
Catalogs count:  3
运行 SQL 查询，可以得到三行数据。

复制代码
mysql> SELECT * FROM catalog;
| id | journal      | publisher       | edition             | title
                    | author      | isPublished |
|  1 | Oracle Magazine | Oracle Publishing | March-April 2005       | Starting w
ith Oracle ADF      | Steve Muench   |        1 |
|  2 | Oracle Magazine | Oracle Publishing | November December 2013 | Engineerin
g as a Service      | David A. Kelly |        1 |
|  3 | Oracle Magazine | Oracle Publishing | November December 2013 | Quintessen
tial and Collaborative | Tom Haunert  |           1 |
3 rows in set (0.00 sec)
mysql>
使用 EntityManager 查找实体
在这一小节，我们将使用 EntityManager 来查找数据。EntityManager 提供了几种方法用来查找数据，如表 2 所示。

方法	描述
find	根据提供的选项查找实体。
findAndCount	根据提供的选项查找并计算实体的个数。分页选项将被忽略。
findByIds	根据提供的 ID 查找实体。
findOne	查找符合选项的第一个实体。
findOneOrFail	查找符合选项的第一个实体，如果没有，则查找失败。
表 2：EntityManager 提供的查找数据的方法
修改 index.ts，以便查找所有的 Catalog 实体。使用 find 方法查找所有 Catalog 实体。

复制代码
createConnection({
  ...
  ...
}).then(async connection => {
   let savedCatalogs = await connection.manager.find(Catalog);
  console.log("All catalogs from the db: ", savedCatalogs);
}).catch(error => console.log(error));
运行应用程序，显示所有的 Catalog 实体（在运行 Repository 之前只会有两个实体）：

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
All catalogs from the db:  [ Catalog {
  id: 1,
  journal: 'Oracle Magazine',
  publisher: 'Oracle Publishing',
  edition: 'March-April 2005',
  title: 'Starting with Oracle ADF',
  author: 'Steve Muench',
  isPublished: true },
  Catalog {
  id: 2,
  journal: 'Oracle Magazine',
  publisher: 'Oracle Publishing',
  edition: 'November December 2013',
  title: 'Engineering as a Service',
  author: 'David A. Kelly',
  isPublished: true } ]
使用 Repository 查找实体
在这一小节，我们将使用 Repository 来查找实体。不过我们需要先安装 class-transformer 包，用于序列化和反序列化 JSON 对象。

复制代码
C:\Typescript\MySQLProject>npm install class-transformer -g
+ class-transformer@0.2.0
added 1 package from 1 contributor in 10.452s
在 index.ts 中获取 Repository 实例，然后是 find 方法查找实体。find 方法的语法如下所示：

复制代码
find(options?: FindManyOptions<Entity>)
FindManyOptioins 可以包含表 3 中所示的一个或多个属性。

属性	描述
cache	启用或禁用查询结果缓存。
join	指定需要加载的关系。
loadEagerRelations	指定是否加载关系实体，默认情况下会加载。
loadRelationIds	指定是否加载关系实体的 ID。如果设置为 true，所有的关系实体 ID 都会被加载，并被映射成关系值。
order	指定实体的排序顺序。
relations	指定要加载的实体关系。
select	指定要查询哪些列。
skip	需要跳过的实体数量。
take	查询的实体的最大数量。
where	指定查询条件。
表 3：FindManyOptions 属性
修改 index.ts，使用 find 方法查找实体。首先，获得一个 Repository 对象，然后使用 find 方法查找所有实体，并且 select 选项只指定 title 和 author 两个列。

复制代码
let allCatalogs = await catalogRepository.find({ select: ["title", "author"] });
使用 serialize 方法序列化 JSON 结果。

复制代码
console.log("All Catalogs from the db: ", serialize(allCatalogs)+'\n');
使用 findOne 方法查找 id 为 1 的实体，并使用 serialize 方法序列化 JSON 结果。

复制代码
let firstCatalog = await catalogRepository.findOne(1);
  console.log('First Catalog from the db: ', serialize(firstCatalog)+'\n');
接下来，使用 findOne 方法查找 title 为”Engineering as a Service“的第一个实体。

复制代码
let specificTitleCatalog = await catalogRepository.findOne({ title: "Engineering as a Service"});
输出 JSON 结果。

复制代码
console.log("'Engineering as a Service' Catalog from the db: ", serialize(specificTitleCatalog)+'\n');
查找所有 edition 为”November December 2013“的实体。

复制代码
let allSpecificEditionCatalogs = await catalogRepository.find({ edition: "November December 2013"});
  console.log('All November December 2013 Catalogs: ', serialize(allSpecificEditionCatalogs)+'\n');
查找所有 isPublished 为 true 的实体。

复制代码
let allPublishedCatalogs = await catalogRepository.find({ isPublished: true });
  console.log('All published Catalogs: ', serialize(allPublishedCatalogs)+'\n');
运行应用程序，输出如下所示：

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
 
All Catalogs from the db:  [{"title":"Starting with Oracle ADF","author":"Steve Muench"},{"title":"Engineering as a Service","author":"David A. Kelly"},{"title":"Quintessential and Collaborative","author":"Tom Haunert"}]
 
First Catalog from the db:  {"id":1,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"March-April 2005","title":"Starting with Oracle ADF","author":"Steve Muench","isPublished":true}
 
'Engineering as a Service' Catalog from the db:  {"id":2,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Engineering as a Service","author":"David A. Kelly","isPublished":true}
 
All November December 2013 Catalogs:  [{"id":2,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Engineering as a Service","author":"David A. Kelly","isPublished":true},{"id":3,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Quintessential and Collaborative","author":"Tom Haunert","isPublished":true}]
 
All published Catalogs:  [{"id":1,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"March-April 2005","title":"Starting with Oracle ADF","author":"Steve Muench","isPublished":true},{"id":2,"journal":"Oracle Magazine","publisher":"Oracle  Publishing","edition":"November December 2013","title":"Engineering as a Service","author":"David A. Kelly","isPublished":true},{"id":3,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Quintessential and Collaborative","author":"Tom Haunert","isPublished":true}]
更新实体
在这一小节，我们将更新 Catalog 实体。EntityManager 和 Repository 都提供了 update 方法用于更新实体。update 方法速度很快很高效，但存在以下两个限制：

更新操作不涉及级联、关系或其他操作。
不验证数据库中是否存在要更新的实体。
save 方法会更新已存在的实体，如果实体不存在则新增，所以 save 方法不存在 update 方法那样的问题。我们将更新一个实体的标题字段，这个实体的 id 为 1，将标题从”Starting with Oracle ADF“更新为”Beginning with Oracle ADF“。

查找 id 为 1 的实体。

复制代码
let catalogToUpdate = await catalogRepository.findOne(1);
将标题字段改为”Beginning with Oracle ADF“。

复制代码
catalogToUpdate.title = "Beginning with Oracle ADF";
使用 save 方法保存实体。

复制代码
await catalogRepository.save(catalogToUpdate);
然后再次查找 id 为 1 的实体。

复制代码
let updatedCatalog  = await catalogRepository.findOne(1);
输出被更新过的结果。

复制代码
console.log('Updated Catalog from the db: ', serialize(updatedCatalog)+'\n');
运行应用程序更新实体，并输出更新过的实体。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
First Catalog from the db:  {"id":1,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"March-April 2005","title":"Beginning with Oracle ADF","author":"Steve Muench","isPublished":true}
删除实体
在这一小节，我们将移除一个实体。EntityManager 和 Repository 都提供了两个方法用于删除实体，如表 4 所示。

方法	描述
delete	根据非空条件删除实体。与 update 方法一样，它不检查要删除的实体是否存在，而且不包括级联和关系。不过这个方法速度也很快很高效。
remove	从数据库中移除一个实体。
表 4：移除实体的方法
修改 index.ts 代码以便演示如何移除实体。

使用 findOne 方法查找 id 为 1 的实体。

复制代码
let catalogToRemove = await catalogRepository.findOne(1);
使用 remove 方法移除实体。

复制代码
await catalogRepository.remove(catalogToRemove);
然后，查找被删除的实体，看看是否已被删除。如果被删除，就不应该输出这个实体。

复制代码
let firstCatalog = await catalogRepository.findOne(1);
    console.log("First Catalog from the db: ", serialize(firstCatalog));
运行应用程序，findOne 的结果是 undefined，说明实体已被删除。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
First Catalog from the db:  undefined
在 MySQL CLI 中运行 SQL 查询，只有一行数据。

复制代码
mysql> SELECT * FROM catalog;
| id | journal       | publisher      | edition             | title
                    | author      | isPublished |
|  2 | Oracle Magazine | Oracle Publishing | November December 2013 | Engineerin
g as a Service      | David A. Kelly |        1 |
|  3 | Oracle Magazine | Oracle Publishing | November December 2013 | Quintessen
tial and Collaborative | Tom Haunert  |           1 |
2 rows in set (0.00 sec)
mysql>
创建实体间的一对一关系
TypeORM 支持几种实体间关系：

一对一
一对多、多对一
多对多
TypeORM 分别为这些关系提供了相应的 Decorator 和函数，如表 5 所示。

函数	描述
OneToOne	指定实体间的一对一关系
JoinColumn	指定一对一关系的所有方
OneToMany	指定实体间的一对多关系
ManyToOne	指定实体间的多对一关系
ManyToMany	指定实体间的多对多关系
JoinTable	指定多对多关系的所有方
表 5：关系函数
在这一小节，我们将讨论实体间的一对一关系。创建第二个实体 CatalogTimestamp，在 entity 目录创建 CatalogTimestamp.ts，并导入额外的 OneToOne 和 JoinColumn 函数。

复制代码
import {Entity, Column, PrimaryGeneratedColumn, OneToOne, JoinColumn} from "typeorm";
import {Catalog} from "./Catalog";
导出 CatalogTimestamp 类，并加上 @Entity()，让它成为一个实体。

复制代码
@Entity()
export class CatalogTimestamp {
…
}
声明主键列 ID。

复制代码
@PrimaryGeneratedColumn()
id: number;
增加列 firstAdd、firstUpdated 和 lastUpdated，所有都是 string 类型。

复制代码
@Column()
  firstAdded: string;
  @Column()
  firstUpdated: string;
 
  @Column()
  lastUpdated: string;
使用 @OneToOne 为 Catalog 添加一对一关系。实体间关系可以是单向也可以是双向的。我们需要指定双向关系。type => Catalog（或者 () => Catalog）将返回一对一关系所对应的实体类。catalog => catalog.timestamp 返回关系的另一方。@JoinColumn() 指定一对一关系的所有方，并且只有其中一方是所有方。

复制代码
@OneToOne(type => Catalog, catalog => catalog.timestamp)
  @JoinColumn()
  catalog: Catalog;
CatalogTimestamp 在GitHub上可以找到，可以直接将其拷贝到 CatalogEntity.ts 中。

我们需要修改 Catalog 实体，并指定它与 CatalogTimestamp 的一对一关系。从 TypeORM 中导入 OneToOne 和 JoinColumn 函数。然后导入 CatalogTimestamp 类。

复制代码
import {Entity, Column, PrimaryGeneratedColumn, OneToOne, JoinColumn} from "typeorm";
import {CatalogTimestamp} from "./CatalogTimestamp";
Catalog 实体的其余部分与之前一样，只是需要再声明一下 @OneToOne。 type => CatalogTimestamp 指定要关联的实体。timestamp => timestamp.catalog 指定反向关系。cascade 设置为 true，当其中一个实体被保存时，另一个对应的实体也会被保存。也就是说，如果保存了 Catalog 的实例，相关联的 CatalogTimestamp 实例也会被保存。

复制代码
@OneToOne(type => CatalogTimestamp, timestamp => timestamp.catalog,{
      cascade: true,
  })
  timestamp: CatalogTimestamp;
接下来，我们需要修改 index.ts，使用 Catalog 和 CatalogTimestamp 实体创建一对一关系。需要额外导入 CatalogTimestamp 类。

复制代码
import {CatalogTimestamp} from "./entity/CatalogTimestamp";
就像之前那样创建一个 Catalog 实体实例。

复制代码
let catalog = new Catalog();
  catalog.journal = "Oracle Magazine";
  catalog.publisher = "Oracle Publishing";
  catalog.edition = "March-April 2005";
  catalog.title = "Starting with Oracle ADF";
  catalog.author = "Steve Muench";
  catalog.isPublished = true;
另外再创建一个 CatalogTimestamp 实体实例。

复制代码
let timestamp = new CatalogTimestamp();
  timestamp.firstAdded = "Apr-8-2014-7:06:16-PM-PDT";
  timestamp.firstUpdated = "Apr-8-2014-7:06:20-PM-PDT";
  timestamp.lastUpdated = "Apr-8-2014-7:06:20-PM-PDT";
关联 CatalogTimestamp 和 Catalog 实体实例。

复制代码
timestamp.catalog = catalog; 
获取 Catalog 的 Repository。

复制代码
let catalogRepository = connection.getRepository(Catalog);
使用 save 方法保存 Catalog 实体。

复制代码
await catalogRepository.save(catalog);
console.log("Catalog has been saved");
修改过的 Catalog.ts 可在GitHub上找到。

运行应用程序，实体以及实体间的关系将会被保存到数据库中。因为指定了 cascade，所以 CatalogTimestamp 实体也会被保存。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
Catalog has been saved
Timestamp is saved, and relation between timestamp and catalog is created in the database too
通过实体间关系来查找对象
在这一小节，我们将通过实体间的关系来查找对象。EntityManager 或 Repository 提供的 find* 方法可用来根据关系查找对象。修改 index.ts，获取 Repository 实例。查找所有实体，包括它们之间的关系。FindManyOptions 的 relations 属性指定了与 timestamp 的关系。

复制代码
let catalogs = await catalogRepository.find({relations: ["timestamp"]});
输出 JSON 结果。

复制代码
console.log(serialize(catalogs));
运行应用程序，输出新增的 Catalog 实体和 CatalogTImestamp 实体。三个 Catalog 实体中只有一个拥有非空的 timestamp。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
 
[{"id":6,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"March-April 2005","title":"Starting with Oracle ADF","author":"Steve Muench","isPublished":true,"timestamp":{"id":1,"firstAdded":"Apr-8-2014-7:06:16-PM-PDT","firstUpdated":"Apr-8-2014-7:06:20-PM-PDT","lastUpdated":"Apr-8-2014-7:06:20-PM-PDT"}},{"id":2,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Engineering as a Service","author":"David A. Kelly","isPublished":true,"timestamp":null},{"id":3,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"November December 2013","title":"Quintessential and Collaborative","author":"Tom Haunert","isPublished":true,"timest
amp":null}]
find* 方法加上 FindOneOptions 和 FindManyOptions 适用于大部分查询，而 QueryBuilder 适用于较复杂的查询，因为它提供了一些选项，如 WHERE 表达式、HAVING 表达式、ORDER BY 表达式、GROUP BY 表达式、LIMIT 表达式和 OFFSET 表达式，还有连接关系，如内连接和外连接、没有选择的连接、连接和映射功能、翻页和子查询。举个例子，创建一个 QueryBuilder，使用 innerJoinAndSelect 指定内连接，并使用 getMany 获得多个结果。如果要获取一个结果，可以使用 getOne。

复制代码
  let catalogs = await connection
            .getRepository(Catalog)
            .createQueryBuilder("catalog")
            .innerJoinAndSelect("catalog.timestamp", "timestamp")
          .getMany();
    console.log(serialize(catalogs));
运行应用程序，输出 Catalog 和 CatalogTimestamp。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
[{"id":6,"journal":"Oracle Magazine","publisher":"Oracle Publishing","edition":"March-April 2005","title":"Starting with Oracle ADF","author":"Steve Muench","isPublished":true,"timestamp":{"id":1,"firstAdded":"Apr-8-2014-7:06:16-PM-PDT","firstUpdated":"Apr-8-2014-7:06:20-PM-PDT","lastUpdated":"Apr-8-2014-7:06:20-PM-PDT
"}}]
列出 mysql 数据库中的数据表，会看到 catalog 和 catalog_timestamp。

复制代码
mysql> use mysql
Database changed
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
| catalog                   |
| catalog_timestamp         |
在 catalog_timestamp 上运行 DESC 命令，除了其他列，还会列出 catalogId 外键。

复制代码
mysql> DESC catalog_timestamp;
+--------------+--------------+------+-----+---------+----------------+
| Field     | Type      | Null | Key | Default | Extra        |
+--------------+--------------+------+-----+---------+----------------+
| id        | int(11)   | NO   | PRI | NULL | auto_increment |
| firstAdded   | varchar(255) | NO   |     | NULL |             |
| firstUpdated | varchar(255) | NO   |  | NULL    |             |
| lastUpdated  | varchar(255) | NO   |     | NULL |             |
| catalogId | int(11)   | YES  | UNI | NULL |             |
+--------------+--------------+------+-----+---------+----------------+
5 rows in set (0.02 sec)
在 catalog_timestamp 上运行 SELECT 查询，查询结果包括值为 6 的外键列，它是 catalog 表的 id，timestamp 就是通过这个 id 与 catalog 进行关联的。

复制代码
mysql> SELECT * FROM catalog_timestamp;
| id | firstAdded                | firstUpdated           | lastUpdated
      | catalogId |
|  1 | Apr-8-2014-7:06:16-PM-PDT | Apr-8-2014-7:06:20-PM-PDT | Apr-8-2014-7:06:2
0-PM-PDT |      6 |
1 row in set (0.00 sec)
在 catalog 上运行 SELECT 查询，会列出值为 6 的 id。

复制代码
mysql> SELECT * FROM catalog;
| id | journal      | publisher       | edition             | title
                    | author      | isPublished |
|  2 | Oracle Magazine | Oracle Publishing | November December 2013 | Engineerin
g as a Service      | David A. Kelly |        1 |
|  3 | Oracle Magazine | Oracle Publishing | November December 2013 | Quintessen
tial and Collaborative | Tom Haunert  |         1 |
|  6 | Oracle Magazine | Oracle Publishing | March-April 2005     | Starting w
ith Oracle ADF      | Steve Muench   |           1 |
3 rows in set (0.00 sec)
类似的，我们也可以创建多对多关系。接下来，我们将介绍一个一对多的例子。

创建一对多关系
为了演示一对多关系，我们仍然需要两个实体。在这一小节中，我们将使用 CatalogEdition 实体，它与 CatalogEntry 实体是一对多的关系。除了主键列 id 之外，CatalogEdition 还指定了 edition 和 isPublished 列。@OneToMany 定义了与 CatalogEntry 的一对多关系，包括反向关系，它们的关系是双向的。

复制代码
@OneToMany(type => CatalogEntry, catalogEntry => catalogEntry.catalogEdition) 
  catalogEntries: CatalogEntry[];
CatalogEdition 实体已经在GitHub上列出。

除了主键列 id 之外，CatalogEntry 还指定了 title、author、isPublished。@ManyToOne 指定了 CatalogEdition 的多对一关系，包括反向关系。

复制代码
@ManyToOne(type => CatalogEdition, catalogEdition => catalogEdition.catalogEntries)
  catalogEdition: CatalogEdition;
CatalogEntry 实体也已经在GitHub上列出。

像下面这样修改 index.ts。

复制代码
import "reflect-metadata";
import {createConnection} from "typeorm";
import {CatalogEdition} from "./entity/Edition";
import {CatalogEntry} from "./entity/Section";
import {serialize} from "class-transformer";
 
createConnection({
  type: "mysql",
  host: "localhost",
  port: 3306,
  username: "root",
  password: "mysql",
  database: "mysql",
  entities: [
      __dirname + "/entity/*.ts"
  ],
  synchronize: true,
  logging: false
}).then(async connection => {
  // Create entity instances and save data to entities
}).catch(error => console.log(error));
运行应用程序，创建表和它们之间的关系。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
列出 mysql 数据库的表，可以看到 catalog_edition 和 catalog_entry 表。

复制代码
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| catalog                   |
| catalog_edition           |
| catalog_entry             |
| catalog_timestamp         |
对 catalog_edition 进行 DESC，将列出 id、edition 和 isPublished 三个列。

复制代码
mysql> DESCRIBE catalog_edition;
| Field   	| Type     	| Null | Key | Default | Extra      	|
| id      	| int(11)      | NO   | PRI | NULL	| auto_increment |
| edition 	| varchar(255) | NO   | 	| NULL    |            	|
| isPublished | tinyint(4)   | NO   | 	| NULL    |            	|
3 rows in set (0.01 sec)
对 catalog_entry 进行 DESC，列出的内容将包括外键 catalogEditionId。

复制代码
mysql> DESCRIBE catalog_entry;
| Field         | Type      | Null | Key | Default | Extra        |
| id            | int(11)   | NO   | PRI | NULL | auto_increment |
| title         | varchar(255) | NO   |   | NULL    |             |
| author        | varchar(255) | NO   |   | NULL    |             |
| isPublished   | tinyint(4)   | NO   |     | NULL  |             |
| catalogEditionId | int(11)      | YES  | MUL | NULL |             |
5 rows in set (0.01 sec)
创建多对多关系
在这一小节，我们将演示创建实体之间的多对多关系，这次仍然需要两个实体。我们将使用两个新的实体 Edition 和 Section。Section 定义了 id 和 name，它与 Edition 的多对多关系是通过 @ManyToMany 来定义的，如下所示。

复制代码
@ManyToMany(type => Edition, edition => edition.sections)
  editions: Edition[];
Section 实体已经在GitHub上列出。

Edition 实体也指定了 id 和 name。@ManyToMany 定义了它与 Section 间的关系，包括反向关系。@JoinTable() 指定了关系的所有方。

复制代码
@ManyToMany(type => Section, section => section.editions)
  @JoinTable()
  sections: Section[];
Edition 实体已经在GitHub上列出。

修改 index.ts，创建两个 Edition 实例，并保存到数据库中。

复制代码
let edition1 = new Edition();
edition1.name = "January February 2019";
await connection.manager.save(edition1);
 
let edition2 = new Edition();
edition2.name = "November December 2018";
await connection.manager.save(edition2);
创建一个 Section 实例。

复制代码
let section = new Section();
section.name = "Application Development";
将 editions 关系设置为 edition1 和 edition2。

复制代码
section.editions = [edition1, edition2];
将 Section 实体保存到数据库。

复制代码
await connection.manager.save(section);
创建另一个 Section 实例，将 editions 设置为 edition1，并保存。

复制代码
let section2 = new Section();
section2.name = "DBA";
section2.editions = [edition1];
await connection.manager.save(section2);
创建第三个 Section 实例，使用关系 edition1，并保存到数据库。使用 findOne 方法查找一个 Section 实例，包括与 editions 关系相关联的 Edition。

复制代码
const loadedSection = await connection
  .getRepository(Section)
  .findOne(1, { relations: ["editions"] });
输出 JSON 结果。

复制代码
console.log(serialize(loadedSection));
修改过的 index.ts 已经在GitHub上列出。

运行应用程序，创建 Edition 和 Section 之间的多对多关系。

复制代码
C:\Typescript\MySQLProject>npm start
> MySQLProject@0.0.1 start C:\Typescript\MySQLProject
> ts-node src/index.ts
{"id":1,"name":"Application Development","editions":[{"id":2,"name":"January February 2019"},{"id":3,"name":"November December 2018"}]}
列出 edition 和 section 表，还可以看到连接表 edition_sections_section。

复制代码
mysql> show tables;
| Tables_in_mysql           |
| edition                   |
| edition_sections_section  |
| section                   |
总结
TypeScript 是 JavaScript 的超集，提供了面向对象的特性，比如接口和静态类型、类型注解和类型推断。TypeScript 还提供了一些实验性的特性，叫作 Decorator，用于给类和类成员添加注解，实现额外的特性。TypeScript 是为现代大规模应用程序而设计的。在这篇文章中，我们介绍了如何使用 TypeScript 操作 MySQL 数据库。TypeORM 库用来连接数据库、创建和保存实体、创建实体间关系、查找实体、更新和删除实体。我们还演示了使用 TypeScript 构建一个端到端的对象关系映射应用程序。

关于作者
Deepak Vohra 是经过 Sun 认证的 Java 程序员和 Web 组件开发人员。Deepak 在 WebLogic Developer’s Journal、XML Journal、ONJava、Java .net、IBM developerWorks、Java Developer’s Journal、Oracle 杂志和 devx 上发表过 Java 和 Java EE 相关的技术文章。Deepak 还出版了一本关于异步 JavaScript 和 XML 的书。

查看英文原文：https://www.infoq.com/articles/typescript-mysql
