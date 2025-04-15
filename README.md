

![image](https://github.com/user-attachments/assets/20a5f4bf-3f6c-40fe-a061-654e98085b6a)

## Filtrar o relatório de crimes
Só há um crime que atenda aos critérios que sabemos

```
SELECT *
FROM crime_scene_report
WHERE date = 20180115 AND type = "murder" AND city = "SQL City"
```

![image](https://github.com/user-attachments/assets/823ec7a6-216f-4486-8558-a8a849f90e52)


## Há duas testemunhas do crime
Pelas descrições, podemos verificar quem exatamente são as testemunhas e verificar seus relatos

```
## Uma testemunha mora na última casa da rua Northwestern Dr
SELECT * 
FROM person
WHERE address_street_name = "Northwestern Dr"
ORDER BY address_number DESC;
```

```
## Uma testemunha mora na Franklin Ave e tem "Annabel" no nome
SELECT * 
FROM person
WHERE address_street_name = "Franklin Ave" AND name LIKE "%Annabel%"
```

As testemunhas são:
id	name	license_id	address_number	address_street_name	ssn
14887	Morty Schapiro	118009	4919	Northwestern Dr	111564949
16371	Annabel Miller	490173	103	Franklin Ave	318771143

## Relatos das testemunhas
Para isso, usamos a query:

```
SELECT *
FROM interview as i
JOIN person as p
ON i.person_id = p.id
WHERE i.person_id = 16371 ## substituir aqui pelo ID da outra testemunha
```

Annabelle:
"I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th."
"I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W""

## Principais dados do crime:
1. A pessoa estava presente na academia no dia 09/01
2. A pessoa é um membro Gold da academia
3. O cadastro da pessoa começa com 48Z
4. A placa do carro da pessoa incluía "H42W"

## Eliminação dos suspeitos
1. 10 pessoas fizeram check-in naquele dia
```
SELECT COUNT(*)
FROM get_fit_now_check_in
WHERE check_in_date = "20180109"
```
2. Há duas pessoas que possuem o cadastro iniciando com "48Z". São elas:
```
SELECT *
FROM get_fit_now_check_in
WHERE check_in_date = "20180109" and membership_id LIKE "%48Z%"
```

48Z7A
48Z55
```
SELECT 
	g.id,
	g.person_id,
	p.name,
	p.license_id
FROM get_fit_now_member as g
JOIN person as p
ON g.person_id = p.id
WHERE g.id = "48Z7A" or g.id = "48Z55"
```
![image](https://github.com/user-attachments/assets/c1332f5b-4916-422e-9aed-1e07001524d4)

3. Verificando os registros de veículos, somente 1 tem sua carteira vinculada a um carro, sendo justamento o carro visto deixando a cena do crime:
```
SELECT 
	d.*,
	p.name	
FROM drivers_license as d
JOIN person as p
ON d.id = p.license_id
WHERE d.id = 423327 or d.id = 173289
```
![image](https://github.com/user-attachments/assets/e01c926a-9a0b-43e4-bbe9-25dd44d6550b)


## Verificando a solução
```
INSERT INTO solution VALUES (1, 'Jeremy Bowers');
        
        SELECT value FROM solution;
```

E a resposta foi:
*Congrats, you found the murderer!*


Desafio extra:


WITH height_number as (
  SELECT
  SUBSTR(
    height,
    INSTR(height, '(') + 1,
    INSTR(height, ')') - INSTR(height, '(') - 1
  ) AS height_num
  FROM drivers_license
)
SELECT
	h.height_num,
	d.hair_color,
	d.car_make,
	d.car_model,
	p.name
FROM height_number as h 
JOIN drivers_license AS d
JOIN person
ON d.id = p.license_id
JOIN facebook_event_checkin AS f
ON p.id = f.person_id









