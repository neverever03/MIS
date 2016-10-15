### 1、power designer设计E-R图：

https://github.com/neverever03/MIS/blob/master/er.PNG

### 2、SQL语句：
CREATE TABLE `材料` (
  `材料号` int(11) NOT NULL,
  `材料` varchar(45) NOT NULL,
  `总量` int(11) NOT NULL,
  `剩余` int(11) NOT NULL,
  PRIMARY KEY (`材料号`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `材料消耗` (
  `记录号` int(11) NOT NULL,
  `材料号` int(11) NOT NULL,
  `消耗数量` int(11) NOT NULL,
  KEY `材料号_idx` (`材料号`),
  KEY `记录号_idx` (`记录号`),
  CONSTRAINT `材料号` FOREIGN KEY (`材料号`) REFERENCES `材料` (`材料号`) ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `记录号` FOREIGN KEY (`记录号`) REFERENCES `检修记录` (`记录号`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `检修` (
  `检修号` int(11) NOT NULL AUTO_INCREMENT,
  `检修日期` varchar(45) NOT NULL,
  `检修人` varchar(45) NOT NULL,
  PRIMARY KEY (`检修号`)
) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8;

CREATE TABLE `检修项目` (
  `设备类别` varchar(45) NOT NULL,
  `项目` varchar(45) NOT NULL,
  PRIMARY KEY (`项目`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `设备类别` (
  `设备类别` varchar(45) NOT NULL,
  `提前期` int(11) NOT NULL,
  `检修周期` int(11) NOT NULL,
  PRIMARY KEY (`设备类别`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `设备` (
  `设备号` int(11) NOT NULL,
  `设备类别` varchar(45) NOT NULL,
  PRIMARY KEY (`设备号`),
  KEY `设备类别_idx` (`设备类别`),
  CONSTRAINT `设备类别` FOREIGN KEY (`设备类别`) REFERENCES `设备类别` (`设备类别`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


CREATE TABLE `检修记录` (
  `记录号` int(11) NOT NULL,
  `检修号` int(11) NOT NULL,
  `设备号` int(11) NOT NULL,
  `项目` varchar(45) NOT NULL,
  `说明` varchar(45) DEFAULT NULL,
  PRIMARY KEY (`记录号`),
  KEY `检修号_idx` (`检修号`),
  KEY `项目_idx` (`项目`),
  CONSTRAINT `检修号` FOREIGN KEY (`检修号`) REFERENCES `检修` (`检修号`) ON DELETE NO ACTION ON UPDATE NO ACTION,
  CONSTRAINT `项目` FOREIGN KEY (`项目`) REFERENCES `检修项目` (`项目`) ON DELETE NO ACTION ON UPDATE NO ACTION
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
 
#### 模拟数据：
 INSERT INTO `检修`.`材料`
(`材料号`,
`材料`,
`总量`,
`剩余`)
VALUES
(1,'线缆',23,3),
(2,'CST触摸屏',23,3),
(3,'按钮',56,45);

INSERT INTO `检修`.`设备类别`
(`设备类别`,
`提前期`,
`检修周期`)
VALUES
('CST',5,30),
('变频器',7,60);

INSERT INTO `检修`.`设备`
(`设备号`,
`设备类别`)
VALUES
(1,'CST'),
(2,'变频器');

INSERT INTO `检修`.`检修`
(`检修号`,
`检修日期`,
`检修人`)
VALUES
(1,2016-10-02,'de'),
(2,2016-10-03,'rt');

INSERT INTO `检修`.`检修记录`
(`记录号`,
`检修号`,
`设备号`,
`项目`,
`说明`)
VALUES
(1,1,1,'检查电磁阀的好坏','hh'),
(2,1,1,'清扫控制箱，紧固接线','hh'),
(3,2,2,'检查CST控制箱内照明灯','dd');

INSERT INTO `检修`.`材料消耗`
(`记录号`,
`材料号`,
`消耗数量`)
VALUES
(1,1,4),(1,2,3),(2,2,1),(3,2,1);

INSERT INTO `检修`.`检修项目`
(`设备类别`,
`项目`)
VALUES
('CST','检查电磁阀的好坏'),
('CST','清扫控制箱，紧固接线'),
('变频器','检查CST控制箱内照明灯');

### 3、根据设备编号查询：
use 检修;
SELECT 设备号,检修日期,检修人,
项目,说明,材料,消耗数量 
FROM 材料消耗 ,检修记录,检修,材料
where 材料消耗.记录号=检修记录.记录号
 and 检修记录.记录号=检修.检修号  
 and 检修记录.设备号=2;
 
#### 截图：
https://github.com/neverever03/MIS/blob/master/%E6%8A%A5%E5%91%8A.PNG

### 4、预警：
   use 检修;
 select distinct  设备号,距离时间 from 
						(select max(检修日期) as maxd,datediff(current_date,max(检修日期)) as 距离时间,设备.设备号,提前期 
						from 设备,设备类别,检修,检修记录
						where 设备.设备号=检修记录.设备号 and 检修记录.检修号=检修.检修号
                        group by 设备.设备号) as t
                        
 where datediff(current_date,maxd)<提前期;
 
 #### 截图：
 https://github.com/neverever03/MIS/blob/master/%E9%A2%84%E8%AD%A6.PNG
 
### 5、AXURE原型设计：
https://github.com/neverever03/MIS/blob/master/Axure1.rp
