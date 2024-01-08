# SQL2_InnerJoin

## Joins
### Allgemeines
Ein Join verbindet Daten aus mehreren Tabellen 

**EMP**
```markdown
| EMPNO | ENAME | ........ | **DEPTNO** |
|-------|-------|----------|------------|
| 7499  | ALLEN | ........ | **30**     |
```

**DEPT**
```markdown
| DEPTNO | DNAME | LOC     |
|--------|-------|---------|
| **30** | SALES | CHICAGO |
```

Wenn wir nun Daten von beiden Tabellen lesen möchten, brauchen wir zwei `SELECT`-Statements.
#### Beispiel
Wo arbeitet `ALLEN`?

1.)
```sql
SELECT DEPTNO
FROM EMP
WHERE ENAME = 'ALLEN';
```

ERGEBNIS: `30`

2.)
```sql
SELECT LOC
FROM DEPT
WHERE DEPTNO = 30
```

Um dies in einem `SELECT`-Statement schreiben zu können gibt es `Join` Schreibweisen, die diese Statements vereinen.

```sql
SELECT Spalten
From Tabelle1, Tabelle 2
WHERE BEDINGUNG
```

In unserem Beispiel
```sql
SELECT LOC
FROM EMP,DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO 
AND EMP.DNAME = 'ALLEN';
```

Mit dem Ausdruck `EMP.DEPTNO = DEPT.DEPTNO` bilden wir die Vereinigungsmenge der Daten aus `EMP` und `DEPT`. Genauer gesagt alle Daten, die die gleiche `DEPTNO` besitzen, werden zu einem Datensatz zusammengefasst.

#### Beispiel 1
Ausgabe aller Mitarbeiter und der ihnen zugeordneten Abteilsungsnamen, die in Abteilung `30` oder `40` arbeiten.

```sql
SELECT DEPT.DEPTNO,DNAME,ENAME,JOB
FROM EMP, DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO
AND EMP.DEPTNO IN (30,40)
ORDER BY DEPT.DEPTNO;
```

### ANSI vs. Oracle Notation
In der Welt der Datenbanken gibt es verschiedene Schreibweisen für die selbe Funktionalität. In MySQL(XAMPP) wird die ANSI Notation unterstützt und in der Oracle DB können ANSI wie auch Oracle Notation verwendet werden. Grundsätzlich unterscheiden sich die Notationen nur in der Schreibweise. Die Funktionalität beider Schreibweisen ist identisch. 

### Cross Join
Entspricht dem [kartesischem Produkt](https://de.wikipedia.org/wiki/Kartesisches_Produkt) zweier Tabellen.

#### Oracle Notation
```sql
SELECT ENAME, DNAME
FROM EMP,DEPT;
```

#### ANSI Notation
```sql
SELECT ENAME,DNAME
FROM EMP CROSS JOIN DEPT;
```

### Inner Join
Entspricht der [Schnittmenge](https://de.wikipedia.org/wiki/Menge_(Mathematik)#Durchschnitt_(Schnittmenge,_Schnitt)) zweier Tabellen.

#### Oracle Notation
```sql
SELECT *
FROM EMP,DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO;
```
In dieser Schreibweise eigentlich Cross-Join mit Filterung.

#### ANSI Notation
```sql
SELECT *
FROM EMP INNER JOIN DEPT
ON EMP.DEPTNO = DEPT.DEPTNO
```

Wenn der Name der Spalten, über die der Join durchgeführt wird, gleich ist, kann alternativ folgende Schreibweise verwendet werden.
```sql
SELECT *
FROM EMP INNER JOIN DEPT
USING (DEPTNO);
```

### Alias
Alias werden verwendet, um Spalten oder Tabellen temporär "umzubenennen".
* Zur leichteren Lesbarkeit
* aus technischen Gründen (Bsp. `FROM DEPT, DEPT`)

#### Spaltenalias
```sql
SELECT column_name [as] alias_name
FROM table_name;
```

#### Tabellen Alias
```sql
SELECT column_name(s)
FROM table_name [as] alias_name
WHERE alias_name.column_name ....;
```

### Self Join
Die Tabelle wird mit sich selbst verbunden. Das heißt, dass ein Eintrag einer Tabelle auf einen anderen Eintrag der selben Tabelle verweist (klassisches Beispiel hierfür ist eine unäre Beziehung).

Als Beispiel hierfür wollen wir wissen, wer für wen arbeitet.
**Employees and Their Managers**
```
| `SCOTT arbeitet für JONES`   |
| `FORD arbeitet für JONES`    |
| `JAMES arbeitet für BLAKE`   |
| `MART

IN arbeitet für BLAKE`  |
| `MILLER arbeitet für CLARK`  |
| `......`                     |
```

#### Oracle Notation
```sql
SELECT e1.ename||' arbeitet für '||e2.ename "Employees and Their Managers"
FROM emp e1, emp e2    
WHERE e1.mgr = e2.empno
```

#### ANSI Notation
```sql
SELECT e1.ename||' arbeitet für '||e2.ename "Employees and Their Managers"
FROM emp e1, emp e2    
WHERE e1.mgr = e2.empno
```

#### Beispiel
Gesucht sind alle Mitarbeiter, die mehr verdienen als ihr Vorgesetzter
```sql
SELECT WORKER.ENAME,WORKER.SAL,MANAGER.ENAME,MANAGER.SAL
FROM EMP WORKER,EMP MANAGER
WHERE WORKER.MGR = MANAGER.EMPNO
AND WORKER.SAL > MANAGER.SAL;
```

### Equi Join
Der Equi Join ist ein Join, der im Join Prädikat den Gleichheitsoperator verwendet.

Die zu verbindenden Spalten müssen den gleichen Datentyp haben.

#### Oracle Notation
```sql
SELECT *
FROM EMP,DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO;
```

#### ANSI Notation
```sql
SELECT *
FROM EMP JOIN DEPT
ON EMP.DEPTNO = DEPT.DEPTNO;
```

### Natural Join
Der Natural Join ist ein Join, der auf Spalten basiert, die denselben Namen haben. Die Join-Spalten müssen kompatible Daten beinhalten. Für eine Join-Spalte darf kein Alias Präfix oder Tabellenname verwendet werden. Der Natural Join ist eine Variante des Equi-Joins.

#### Oracle Notation
```sql
SELECT DNAME, ENAME
FROM DEPT, EMP
WHERE DEPT.DEPTNO = EMP.DEPTNO;
```

#### ANSI Notation
```sql
SELECT DNAME, ENAME
FROM DEPT NATURAL JOIN EMP;
```

#### Beispiel
Gesucht ist die Gehaltsstufe jedes Mitarbeiters.
`SALGRADE(GRADE,LOSAL,HISAL)`

##### Oracle Notation
```sql
SELECT GRADE,JOB,ENAME,SAL
FROM EMP,SALGRADE
WHERE SAL BETWEEN LOSAL AND HISAL
ORDER BY GRADE,JOB;
```

##### ANSI Notation
```sql
SELECT GRADE,JOB,ENAME,SAL
FROM EMP JOIN SALGRADE
ON SAL BETWEEN LOSAL AND HISAL
```

### Join von 3 Tabellen
Um mehrere Tabellen zu joinen, verwendet man das gleiche System, wie bei zwei Tabellen.

Gesucht sind Gehaltsstufe, Name, Gehalt und Arbeitsort aller Beschäftigten.

#### Oracle Notation
```sql
SELECT GRADE, ENAME, SAL, LOC
FROM EMP, SALGRADE, DEPT
where  SAL BETWEEN LOSAL AND HISAL
AND DEPT.DEPTNO = EMP.DEPTNO;
```

#### ANSI Notation
```sql
SELECT GRADE, ENAME, SAL, LOC
FROM EMP 
JOIN SALGRADE ON SAL BETWEEN LOSAL AND HISAL
JOIN DEPT ON DEPT.DEPTNO = EMP.DEPTNO;
```
