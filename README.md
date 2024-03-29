# sample-code-collection

- [sample-code-collection](#sample-code-collection)
  - [Documentation](#documentation)
  - [TypeScript](#typescript)
  - [JS](#js)
  - [Webpack](#webpack)
  - [SQL](#sql)
  - [PHP](#php)
  - [Linux commands](#linux-commands)
  - [Author](#author)
  - [License](#license)

## Documentation
* [Changelog](CHANGELOG.md)

## TypeScript
- How to initialize the types object variable with an empty object.  
    1st way) Initialize with an empty object using Type Assertion.  
    ```ts
    type Animal1 = {
      name: string;
      age: number;
    };
    const animal1 = {} as Animal1;
    console.log('animal1=', animal1);// =>animal1= {}
    animal1.name = 'Tom';
    animal1.age = 3;
    console.log('animal1=', animal1);// =>animal1= { name: 'Tom', age: 3 }
    ```
    
    2st way) Initialize with an empty object using Partial Utility Types.
    ```ts
    type Animal2 = {
      name: string;
      age: number;
    };
    const animal2: Partial<Animal2> = {};
    console.log('animal2=', animal2);// =>animal2= {}
    animal2.name = 'Tom';
    console.log('animal2=', animal2);// =>animal2= { name: 'Tom' }
    ```

## JS
- Get a domain name.
    ```js
    function getDomainName() {
      let domain = document.domain;
      const parts = document.domain.split('.');
      const testCookieKey = +(new Date());
      for(var i=2,l=parts.length; i<=l; i++) {
        // Cookie for domain existence determination.
        const testDomain = parts.slice(-i).join('.');
        document.cookie = testCookieKey + '=1;domain=' + testDomain + ';';
        if (document.cookie.indexOf(testCookieKey + '=1') !== -1) {
          // If the cookie is written correctly, the domain exists.
          domain = testDomain;

          // Delete cookies for checking.
          document.cookie = testCookieKey + '=;expires=Thu, 01 Jan 1970 00:00:01 GMT;domain='+ testDomain + ';';
          break;
        }
      }
      return domain;
    }

    // Domain on the "https://docs.google.com/document/d/xxx" page.
    getDomainName();
    // =>'google.com'
    ```
- Provide a timeout in XMLHttpRequest.
    ```js
    function request(url, timeoutMSec) {
      const xhr = new XMLHttpRequest();
      xhr.onload = function () {
        console.log('Success');
        const script = document.createElement('script');
        script.type = 'text/javascript';
        script.text = xhr.responseText;
        document.body.appendChild(script);
      };
      xhr.onabort = () => console.log('Abor');
      xhr.open('GET', url);
      xhr.send(null);
      setTimeout(() => {
        console.log('Do abort');
        xhr.abort();
        // xhr.readyState < 4 && xhr.abort();
      }, timeoutMSec);
    }
    ```
- jQuery Validate Plugin.  
    Customize the class given in case of error and the position of error message display.
    ```js
    $.validator.setDefaults({
      errorClass: 'error',
      errorPlacement:(error, element) => {
        error.insertAfter(element.data('validateErrorPlacement') || element);
      }
    });
    ```

    Display error messages at any given time.
    ```js
    const nameAttr = 'name';
    $form.validate().showErrors({[nameAttr]: 'Error message'});
    ```
- moment.
    The difference between the two time periods.  
    ```js
    const startTime = moment('12:00:00', 'HH:mm:ss');
    const endTime = moment('13:00:00', 'HH:mm:ss');
    
    endTime.diff(startTime, 'sedonds');// =>3600000
    endTime.diff(startTime, 'minutes');// =>60
    endTime.diff(startTime, 'hours');// =>1

    // or
    const duration = moment.duration(endTime.diff(startTime));
    duration.asSeconds();// =>3600
    duration.asMinutes();// =>60
    duration.asHours();// =>1
    ```

    Converts seconds to 'HH:mm:ss' format.  
    ```js
    const seconds = 45296;
    moment.utc(seconds * 1000).format('HH:mm:ss');// =>12:34:56
    ```

    Converts 'HH:mm:ss' format to seconds.
    ```js
    const time = '01:00:00';
    const duration = moment.duration(time);
    duration.asSeconds();// =>3600
    ```

    Convert seconds to hours and minutes.
    ```js
    const seconds = 3660;// '01:01:00'
    const duration = moment.duration(seconds, 'seconds');
    const hours = Math.floor(duration.asHours());
    const minutes = Math.floor(duration.asMinutes() - hours * 60);
    hours;// =>1
    minutes;// =>1
    ```

    Converts seconds to minutes.
    ```js
    const seconds = 3660;// '01:01:00'
    const duration = moment.duration(seconds, 'seconds');
    const minutes = Math.floor(duration.asMinutes());
    minutes;// =>61
    ```
- Converts an object array to an object whose keys are the values of specific elements of the object.
    ```js
    const list = [
      {id: 101, name: 'John'},
      {id: 102, name: 'Robin'},
      {id: 103, name: 'Jane'},
    ];
    const obj = Object.fromEntries(list.map(row => {
      const id = row.id;
      delete row.id;
      return [id, row];
    }));
    console.log(obj);
    // {
    //   101: {name: 'John'}
    //   102: {name: 'Robin'}
    //   103: {name: 'Jane'}
    // }
    ```

## Webpack
- Do arbitrary processing each time you bundle in build and watch.  
    webpack.config.js:
    ```js
    const webpack = require('webpack');

    module.exports = {
      plugins: [
        new webpack.ProgressPlugin(percentage => {
          if (percentage === 1) {
            // Bundling is complete.
          }
        }),
      ],
    }
    ```

## SQL
- Record SQL execution history.  
    Change configuration.
    /etc/my.cnf.d/server.cnf:
    ```text
    [mariadb]
    general_log=ON
    general_log_file=/var/log/mysql/sql.log
    ```

    Restart DB to reflect settings.
    ```sh
    Restart DB to reflect settings.
    ```

    Check SQL execution history.
    ```sh
    sudo tail -f /var/log/mysql/sql.log
    ```
- Deadlock Survey Methods.  
    Check the number of locks occurring.  
    ```sql
    SELECT trx_rows_locked FROM information_schema.INNODB_TRX;
    ```

    Check the number of locks occurring and the thread ID.  
    row lock(s): Number of locks occurring.  
    MySQL thread id: ID of the thread in which the lock is occurring *thread id is the same as the Id in show processlist;.
    ```sql
    SHOW ENGINE INNODB STATUS\G;
    ```
- Transaction isolation level confirmation.
    ```sql
    SELECT @@GLOBAL.tx_isolation, @@tx_isolation;
    -- +-----------------------+-----------------+
    -- | @@GLOBAL.tx_isolation | @@tx_isolation  |
    -- +-----------------------+-----------------+
    -- | REPEATABLE-READ       | REPEATABLE-READ |
    -- +-----------------------+-----------------+
    ```
- Dump only DDL.
    ```sh
    mysqldump \
    -h <host> \
    -u <user> \
    -p<password> \
    -B <db> \
    -d | sed 's/ AUTO_INCREMENT=[0-9]*\b//'
    ```
- DDL and dump data.
    ```sh
    mysqldump \
    -h <host> \
    -u <user> \
    -p<password> \
    --skip-comments \
    <db>
    ```
- Dump data from a specific table.
    ```sh
    mysqldump \
    -h <host> \
    -u <user> \
    -p<password> \
    --no-create-info \
    --complete-insert \
    <db> \
    --where "id=1" <table>
    ```
- Calendar.
    ```sql
    SET @month = '202210';
    SELECT
      td.date
    FROM
      (SELECT
        date_add(CONCAT(@month, '01'), interval td.0 day) date
      FROM
        (VALUES (0), (1), (2), (3), (4), (5), (6), (7), (8), (9), (10),
                (11), (12), (13), (14), (15), (16), (17), (18), (19), (20),
                (21), (22), (23), (24), (25), (26), (27), (28), (29), (30)
        ) td
      ) td
    WHERE 
      DATE_FORMAT(td.date, '%Y%m') = @month;
    -- +------------+
    -- | date       |
    -- +------------+
    -- | 2022-10-01 |
    -- ...
    -- | 2022-10-31 |
    -- +------------+
    ```
- Get the largest record in the same group.  
    Create sample tables.
    ```sql
    DROP TABLE IF EXISTS sample;
    CREATE TABLE sample (
      id int(10) unsigned NOT NULL AUTO_INCREMENT,
      cat enum('a', 'b', 'c') NOT NULL,
      createAt datetime NOT NULL,
      PRIMARY KEY (id)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

    INSERT INTO sample(cat, createAt) VALUES
      ('a', '2022-01-01 00:00:00'),
      ('a', '2022-01-02 00:00:00'),
      ('a', '2022-01-03 00:00:00'),
      ('b', '2022-02-01 00:00:00'),
      ('b', '2022-02-02 00:00:00'),
      ('b', '2022-02-03 00:00:00'),
      ('c', '2022-03-02 00:00:00'),
      ('c', '2022-03-03 00:00:00');
    ```

    Get the record with the largest date in the group.  
    If we change our mindset and consider the concept of "largest" to be "no record larger than the corresponding record," we can write the following.
    ```sql
    SELECT * FROM sample t1 WHERE NOT EXISTS (SELECT 1 FROM sample t2 WHERE t1.cat = t2.cat AND t1.createAt < t2.createAt);
    -- +----+-----+---------------------+
    -- | id | cat | createAt            |
    -- +----+-----+---------------------+
    -- |  3 | a   | 2022-01-03 00:00:00 |
    -- |  6 | b   | 2022-02-03 00:00:00 |
    -- |  8 | c   | 2022-03-03 00:00:00 |
    -- +----+-----+---------------------+
    ```

    Alternatively, the correct data can be obtained by finding the record with the largest value in a subquery and matching it against it.  
    ```sql
    SELECT * FROM sample t1 WHERE createAt = (SELECT MAX(createAt) FROM sample t2 WHERE t1.cat = t2.cat);
    -- +----+-----+---------------------+
    -- | id | cat | createAt            |
    -- +----+-----+---------------------+
    -- |  3 | a   | 2022-01-03 00:00:00 |
    -- |  6 | b   | 2022-02-03 00:00:00 |
    -- |  8 | c   | 2022-03-03 00:00:00 |
    -- +----+-----+---------------------+
    ```
- Get the number of records per ID in another table linked by ID.
    ```sql
    SELECT
      parent.id,
      COALESCE(child.cnt, 0) cnt
    FROM
      parent
      LEFT JOIN(SELECT parentId, COUNT(*) cnt FROM child GROUP BY parentId) child ON parent.id = child.parentId;
    ```
- Check the size (megabytes) of each DB.
    ```sql
    SELECT 
      table_schema, sum(data_length) / 1024 / 1024 size
    FROM 
      information_schema.tables  
    WHERE
      table_schema NOT IN('mysql', 'information_schema', 'performance_schema')
    GROUP BY 
      table_schema
    ORDER BY
      SUM(data_length+index_length) DESC;
    -- +----------------------------+------------+
    -- | table_schema               | size       |
    -- +----------------------------+------------+
    -- | sample                     | 0.04687500 |
    -- +----------------------------+------------+
    ```
- Check the number of records and size (megabytes) of each table.
    ```sql
    SELECT
      table_name,
      table_rows numberOfRecords,
      avg_row_length averageRecordLength,
      (data_length+index_length) / 1024 / 1024 totalSize,
      data_length / 1024 / 1024 dataSize,
      index_length / 1024 / 1024 indexSize
    FROM 
      information_schema.tables
    WHERE
      table_schema=database()
    ORDER BY
      (data_length+index_length) DESC;
    -- +------------+-----------------+---------------------+------------+------------+------------+
    -- | table_name | numberOfRecords | averageRecordLength | totalSize  | dataSize   | indexSize  |
    -- +------------+-----------------+---------------------+------------+------------+------------+
    -- | sample     |               8 |                2048 | 0.01562500 | 0.01562500 | 0.00000000 |
    -- +------------+-----------------+---------------------+------------+------------+------------+
    ```
- Delete records created one month ago.
    ```sql
    DELETE FROM sample WHERE createAt < DATE_SUB(CURDATE(), INTERVAL 1 MONTH);
    ```
- Find the parent or children of a tree structure recursively.  
    <img src="tree-structure.png" width="400">

    Create tree data with the same contents as above.
    ```sql
    CREATE TABLE tree (
      id CHAR(1) NOT NULL PRIMARY KEY,
      parent CHAR(1)
    );

    INSERT INTO tree VALUES
      ('A', null),
      ('B', 'A'),
      ('C', 'A'),
      ('D', 'C'),
      ('E', 'C'),
      ('F', 'E'),
      ('G', 'E'),
      ('H', 'G'),
      ('I', 'G');
    ```

    A query tracing the ancestry of 'I'.
    ```sql
    WITH RECURSIVE ancestor(depth, id, parent) AS (
      SELECT 0, tree.id, tree.parent FROM tree WHERE tree.id = 'I'
      UNION ALL
      SELECT ancestor.depth + 1, tree.id, tree.parent FROM ancestor, tree WHERE ancestor.parent = tree.id
    )
    SELECT depth, id FROM ancestor ORDER BY depth;
    +-------+------+
    | depth | id   |
    +-------+------+
    |     0 | I    |
    |     1 | G    |
    |     2 | E    |
    |     3 | C    |
    |     4 | A    |
    +-------+------+
    ```

    Query to get the children of 'C'.
    ```sql
    WITH RECURSIVE child (depth, id, parent) AS (
      SELECT 0, tree.id, tree.parent FROM tree WHERE tree.id = 'C'
      UNION ALL
      SELECT child.depth + 1, tree.id, tree.parent FROM tree, child WHERE tree.parent = child.id
    )
    SELECT depth, id FROM child ORDER BY depth;
    +-------+------+
    | depth | id   |
    +-------+------+
    |     0 | C    |
    |     1 | D    |
    |     1 | E    |
    |     2 | F    |
    |     2 | G    |
    |     3 | H    |
    |     3 | I    |
    +-------+------+
    ```

## PHP
- Exclusion Control.
    ```php
    const LOCK_FILE = '.lock';
    try {
      // If it is locked, this thread does nothing because another thread is doing the completion operation.
      $fp = fopen(LOCK_FILE, 'a');

      // Lock the completion operations so that they are not executed at the same time.
      if (!flock($fp, LOCK_EX|LOCK_NB))
        return;

      // Do your processing here.
    } finally {
      if (isset($fp)) fclose($fp);
    }
    ```
- Unserialize the session.
    ```php
    function unserialize(string $session): array {
      $unserialized = [];
      $variables = preg_split('/([a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*)\|/', $session, -1, PREG_SPLIT_NO_EMPTY | PREG_SPLIT_DELIM_CAPTURE);
      // $variables = preg_split('/([a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff^|]*)\|/', $session, -1, PREG_SPLIT_NO_EMPTY | PREG_SPLIT_DELIM_CAPTURE);
      for($i=0; $variables[$i]; $i++)
        $unserialized[$variables[$i++]] = unserialize($variables[$i])
      return $unserialized;
    }
    ```
- Get hourly time.
    ```php
    $startTime = new DateTime(date('Y-m-d 00:00:00'));
    $endTime = (new DateTime(date('Y-m-d 00:00:00')))->modify('+1 days');

    // Loop interval; PT1H is every hour.
    $interval = new DateInterval('PT1H');

    // Hourly output.
    $daterange = new DatePeriod($startTime, $interval, $endTime);
    foreach($daterange as $date)
      echo $date->format('Y-m-d H:i') . PHP_EOL;
    // 2022-10-13 00:00
    // ...
    // 2022-10-13 23:00
    ```

## Linux commands
- Find the 10 processes that consume the most memory.  
    &quot;-%mem&quot; means descending order, memory usage.
    - vsize: Memory usage including virtual memory.
    - rss: Real memory usage.
    - pcpu: CPU utilization.
    ```sh
    watch "ps -axo rss,vsize,pcpu,command --sort -%mem|head -n 11"
    ```

- Find the 10 processes with the highest CPU utilization.
    ```sh
    watch "ps -axo rss,vsize,pcpu,command --sort -%cpu|head -n 11"
    ```

## Author
**Takuya Motoshima**

* [github/takuya-motoshima](https://github.com/takuya-motoshima)
* [twitter/TakuyaMotoshima](https://twitter.com/TakuyaMotoshima)
* [facebook/takuya.motoshima.7](https://www.facebook.com/takuya.motoshima.7)

## License
[MIT](LICENSE)