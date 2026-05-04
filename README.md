# Dase-Study-on-Data-Cleaning-Using-SQL

# Laptop Data Cleaning - SQL Case Study

## Questions and Answers


```sql

Q1
Question: How do you view all records from the laptops table?
Answer:
SELECT * FROM laptops;


Q2
Question: How do you create a backup table called laptops_backup with the same structure as laptops?
Answer:
CREATE TABLE laptops_backup LIKE laptops;

Q3
Question: How do you insert all data from laptops into laptops_backup?
Answer:
INSERT INTO laptops_backup SELECT * FROM laptops;

Q4
Question: How do you check the data length of the laptops table in KB?
Answer:
SELECT DATA_LENGTH/1024 FROM information_schema.TABLES 
WHERE TABLE_SCHEMA = 'sql_cx_live' AND TABLE_NAME = 'laptops';

Q5
Question: How do you drop the column named Unnamed: 0 from laptops table?
Answer:ALTER TABLE laptops DROP COLUMN `Unnamed: 0`;

Q6
Question: How do you delete rows where all key specification columns are NULL?
Answer:
DELETE FROM laptops 
WHERE `index` IN (SELECT `index` FROM laptops 
WHERE Company IS NULL AND TypeName IS NULL AND Inches IS NULL 
AND ScreenResolution IS NULL AND Cpu IS NULL AND Ram IS NULL 
AND Memory IS NULL AND Gpu IS NULL AND OpSys IS NULL 
AND Weight IS NULL AND Price IS NULL);

Q7
Question: How do you modify the Inches column to DECIMAL with one decimal place?
Answer:
ALTER TABLE laptops MODIFY COLUMN Inches DECIMAL(10,1);

Q8
Question: How do you remove the 'GB' text from Ram column values?
Answer:
UPDATE laptops l1 
SET Ram = (SELECT REPLACE(Ram,'GB','') FROM laptops l2 WHERE l2.index = l1.index);

Q9
Question: How do you change the Ram column data type to INTEGER?
Answer:
ALTER TABLE laptops MODIFY COLUMN Ram INTEGER;

Q10
Question: How do you remove 'kg' from Weight column values?
Answer:
UPDATE laptops l1 
SET Weight = (SELECT REPLACE(Weight,'kg','') FROM laptops l2 WHERE l2.index = l1.index);

Q11
Question: How do you round the Price column to nearest integer?
Answer:
UPDATE laptops l1 
SET Price = (SELECT ROUND(Price) FROM laptops l2 WHERE l2.index = l1.index);

Q12
Question: How do you change Price column to INTEGER data type?
Answer:
ALTER TABLE laptops MODIFY COLUMN Price INTEGER;

Q13
Question: How do you view distinct operating system values in the laptops table?
Answer:
SELECT DISTINCT OpSys FROM laptops;

Q14
Question: How do you categorize operating systems into macos, windows, linux, N/A, and other using CASE statement?
Answer:
SELECT OpSys,
CASE 
    WHEN OpSys LIKE '%mac%' THEN 'macos'
    WHEN OpSys LIKE 'windows%' THEN 'windows'
    WHEN OpSys LIKE '%linux%' THEN 'linux'
    WHEN OpSys = 'No OS' THEN 'N/A'
    ELSE 'other'
END AS 'os_brand'
FROM laptops;

Q15
Question: How do you update OpSys column with standardized brand names?
Answer:
UPDATE laptops 
SET OpSys = CASE 
    WHEN OpSys LIKE '%mac%' THEN 'macos'
    WHEN OpSys LIKE 'windows%' THEN 'windows'
    WHEN OpSys LIKE '%linux%' THEN 'linux'
    WHEN OpSys = 'No OS' THEN 'N/A'
    ELSE 'other'
END;

Q16
Question: How do you add two new columns gpu_brand and gpu_name after the Gpu column?
Answer:
ALTER TABLE laptops 
ADD COLUMN gpu_brand VARCHAR(255) AFTER Gpu,
ADD COLUMN gpu_name VARCHAR(255) AFTER gpu_brand;

Q17
Question: How do you extract the first word from Gpu column as gpu_brand?
Answer:
UPDATE laptops l1 
SET gpu_brand = (SELECT SUBSTRING_INDEX(Gpu,' ',1) FROM laptops l2 WHERE l2.index = l1.index);

Q18
Question: How do you remove the gpu_brand from Gpu value to get gpu_name?
Answer:
UPDATE laptops l1 
SET gpu_name = (SELECT REPLACE(Gpu,gpu_brand,'') FROM laptops l2 WHERE l2.index = l1.index);

Q19
Question: How do you drop the original Gpu column?
Answer:
ALTER TABLE laptops DROP COLUMN Gpu;

Q20
Question: How do you add cpu_brand, cpu_name, and cpu_speed columns after Cpu column?
Answer:
ALTER TABLE laptops 
ADD COLUMN cpu_brand VARCHAR(255) AFTER Cpu,
ADD COLUMN cpu_name VARCHAR(255) AFTER cpu_brand,
ADD COLUMN cpu_speed DECIMAL(10,1) AFTER cpu_name;

Q21
Question: How do you extract cpu_brand as the first word from Cpu column?
Answer:
UPDATE laptops l1 
SET cpu_brand = (SELECT SUBSTRING_INDEX(Cpu,' ',1) FROM laptops l2 WHERE l2.index = l1.index);

Q22
Question: How do you extract cpu_speed as decimal by removing 'GHz' from last part of Cpu column?
Answer:
UPDATE laptops l1 
SET cpu_speed = (SELECT CAST(REPLACE(SUBSTRING_INDEX(Cpu,' ',-1),'GHz','') AS DECIMAL(10,2)) 
FROM laptops l2 WHERE l2.index = l1.index);

Q23
Question: How do you extract cpu_name by removing cpu_brand and speed from Cpu column?
Answer:
UPDATE laptops l1 
SET cpu_name = (SELECT REPLACE(REPLACE(Cpu,cpu_brand,''),SUBSTRING_INDEX(REPLACE(Cpu,cpu_brand,''),' ',-1),'') 
FROM laptops l2 WHERE l2.index = l1.index);

Q24
Question: How do you drop the original Cpu column?
Answer:
ALTER TABLE laptops DROP COLUMN Cpu;

Q25
Question: How do you extract width and height from ScreenResolution column?
Answer:
SELECT ScreenResolution,
SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',1),
SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',-1)
FROM laptops;

Q26
Question: How do you add resolution_width and resolution_height columns?
Answer:
ALTER TABLE laptops 
ADD COLUMN resolution_width INTEGER AFTER ScreenResolution,
ADD COLUMN resolution_height INTEGER AFTER resolution_width;

Q27
Question: How do you update resolution_width and resolution_height from ScreenResolution?
Answer:
UPDATE laptops 
SET resolution_width = SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',1),
    resolution_height = SUBSTRING_INDEX(SUBSTRING_INDEX(ScreenResolution,' ',-1),'x',-1);

Q28
Question: How do you add a touchscreen column?
Answer:
ALTER TABLE laptops ADD COLUMN touchscreen INTEGER AFTER resolution_height;

Q29
Question: How do you check if ScreenResolution contains the word Touch?
Answer:
SELECT ScreenResolution LIKE '%Touch%' FROM laptops;

Q30
Question: How do you update touchscreen column based on ScreenResolution having Touch?
Answer:
UPDATE laptops SET touchscreen = ScreenResolution LIKE '%Touch%';

Q31
Question: How do you drop the ScreenResolution column?
Answer:
ALTER TABLE laptops DROP COLUMN ScreenResolution;

Q32
Question: How do you keep only first two words of cpu_name?
Answer:
UPDATE laptops SET cpu_name = SUBSTRING_INDEX(TRIM(cpu_name),' ',2);

Q33
Question: How do you add memory_type, primary_storage, and secondary_storage columns?
Answer:
ALTER TABLE laptops 
ADD COLUMN memory_type VARCHAR(255) AFTER Memory,
ADD COLUMN primary_storage INTEGER AFTER memory_type,
ADD COLUMN secondary_storage INTEGER AFTER primary_storage;

Q34
Question: How do you determine memory_type based on SSD, HDD, or Flash Storage keywords?
Answer:
SELECT Memory,
CASE
    WHEN Memory LIKE '%SSD%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    WHEN Memory LIKE '%SSD%' THEN 'SSD'
    WHEN Memory LIKE '%HDD%' THEN 'HDD'
    WHEN Memory LIKE '%Flash Storage%' THEN 'Flash Storage'
    WHEN Memory LIKE '%Hybrid%' THEN 'Hybrid'
    WHEN Memory LIKE '%Flash Storage%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    ELSE NULL
END AS 'memory_type'
FROM laptops;

Q35
Question: How do you update memory_type using the CASE logic?
Answer:
UPDATE laptops 
SET memory_type = CASE
    WHEN Memory LIKE '%SSD%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    WHEN Memory LIKE '%SSD%' THEN 'SSD'
    WHEN Memory LIKE '%HDD%' THEN 'HDD'
    WHEN Memory LIKE '%Flash Storage%' THEN 'Flash Storage'
    WHEN Memory LIKE '%Hybrid%' THEN 'Hybrid'
    WHEN Memory LIKE '%Flash Storage%' AND Memory LIKE '%HDD%' THEN 'Hybrid'
    ELSE NULL
END;

Q36
Question: How do you extract numeric values for primary and secondary storage from Memory column?
Answer:
SELECT Memory,
REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',1),'[0-9]+'),
CASE WHEN Memory LIKE '%+%' 
     THEN REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',-1),'[0-9]+') 
     ELSE 0 
END
FROM laptops;

Q37
Question: How do you update primary_storage and secondary_storage with extracted numbers?
Answer:
UPDATE laptops 
SET primary_storage = REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',1),'[0-9]+'),
    secondary_storage = CASE WHEN Memory LIKE '%+%' 
                            THEN REGEXP_SUBSTR(SUBSTRING_INDEX(Memory,'+',-1),'[0-9]+') 
                            ELSE 0 
                       END;

Q38
Question: How do you convert storage values from TB to GB if value is 2 or less?
Answer:
SELECT primary_storage,
CASE WHEN primary_storage <= 2 THEN primary_storage*1024 ELSE primary_storage END,
secondary_storage,
CASE WHEN secondary_storage <= 2 THEN secondary_storage*1024 ELSE secondary_storage END
FROM laptops;

Q39
Question: How do you update primary_storage and secondary_storage converting TB to GB?
Answer:
UPDATE laptops 
SET primary_storage = CASE WHEN primary_storage <= 2 THEN primary_storage*1024 ELSE primary_storage END,
    secondary_storage = CASE WHEN secondary_storage <= 2 THEN secondary_storage*1024 ELSE secondary_storage END;

Q40
Question: How do you drop the gpu_name column?
Answer:
ALTER TABLE laptops DROP COLUMN gpu_name;
