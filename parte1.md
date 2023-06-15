# Parte 1 – Personalizando acessos com views

## CODE 1

1. Criar uma nova tabela chamada "users" para armazenar as informações dos usuários. Certifique-se de incluir os campos como "id", "username" e "password".

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  username VARCHAR(50),
  password VARCHAR(50)
);
```

2. Inserir o usuário gerente na tabela "users" com um nome de usuário e senha.
```sql
INSERT INTO users (username, password)
VALUES ('gerente', 'senha');
```

3. Conceder privilégios específicos para o usuário gerente nas tabelas "employee" e "department".
```sql
GRANT SELECT ON database_name.employee TO 'gerente'@'localhost';
GRANT SELECT ON database_name.department TO 'gerente'@'localhost';
```

4. Para restringir o acesso do usuário "employee" apenas às informações de "employee" e negar o acesso aos dados relacionados a departamentos ou gerentes, você pode conceder permissões limitadas apenas à tabela "employee".
```sql
GRANT SELECT ON database_name.employee TO 'employee'@'localhost';
```

5. Para responder às consultas específicas que você mencionou:

- Número de empregados por departamento e localidade:
```sql
SELECT department_name, location, COUNT(*) AS num_employees
FROM department
JOIN employee ON department.id = employee.department_id
GROUP BY department_name, location;
```

- Lista de departamentos e seus gerentes:
```sql
SELECT department_name, CONCAT(manager.first_name, ' ', manager.last_name) AS manager_name
FROM department
JOIN employee AS manager ON department.manager_id = manager.id;
```

- Projetos com maior número de empregados (por ordenação decrescente):
```sql
SELECT project_name, COUNT(*) AS num_employees
FROM project
JOIN employee_project ON project.id = employee_project.project_id
GROUP BY project_name
ORDER BY num_employees DESC;
```

- Lista de projetos, departamentos e gerentes:
```sql
SELECT project_name, department_name, CONCAT(manager.first_name, ' ', manager.last_name) AS manager_name
FROM project
JOIN department ON project.department_id = department.id
JOIN employee AS manager ON department.manager_id = manager.id;
```

- Quais empregados possuem dependentes e se são gerentes:
```sql
SELECT e.first_name, e.last_name, IFNULL(d.dependent_count, 0) AS dependent_count, IF(e.manager_id IS NOT NULL, 'Yes', 'No') AS is_manager
FROM employee AS e
LEFT JOIN (
    SELECT employee_id, COUNT(*) AS dependent_count
    FROM dependent
    GROUP BY employee_id
) AS d ON e.id = d.employee_id;
```

