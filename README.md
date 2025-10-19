# DIO.Randstad-Analise_de_Dados.Construindo-seu-Primeiro-Projeto-L-gico-de-Banco-de-Dados.OFICINA
Queries + PNG da OFICINA, usado o conhecimento da aula, apenas foi usado o uso de IA para compreendimento de assuntos e para o teste de insert, delete e update, feito no curso da DIO: Randstad-Analise_de_Dados
<img width="891" height="454" alt="OFICINA" src="https://github.com/user-attachments/assets/1aacc6d2-e90c-4e9b-9ede-30607106cbf5" />
Query:
drop database if exists oficina;
create database oficina;
use oficina;

create table Cliente(
    idCliente INT auto_increment primary key,
    Nome VARCHAR(45) not null,
    Telefone VARCHAR(45),
    Endereco VARCHAR(45)
);

create table Veiculo(
    idVeiculo INT auto_increment primary key,
    Ano INT,
    Placa VARCHAR(45),
    Modelo VARCHAR(45),
    Cliente_idCliente INT,
    foreign key (Cliente_idCliente) references Cliente(idCliente)
);

create table Mecanico(
    idMecanico INT auto_increment primary key,
    Nome VARCHAR(45) not null,
    Codigo VARCHAR(45),
    Endereco VARCHAR(45),
    Especialidade VARCHAR(45)
);

create table Servico(
    idServico INT auto_increment primary key,
    Descricao VARCHAR(45) not null,
    ValorMaoDeObra DOUBLE
);

create table Avaliacao(
    idAvaliacao INT auto_increment primary key,
    DataAvaliacao DATE,
    Nota VARCHAR(45),
    Comentario VARCHAR(255)
);

create table OS(
    idOS INT auto_increment primary key,
    DataEmissao DATE,
    DataEntrega DATE,
    ValorTotal DOUBLE,
    Status_ ENUM('Em andamento', 'Concluido', 'Cancelado') default 'Em andamento',
    Veiculo_idVeiculo INT,
    Avaliacao_idAvaliacao INT,
    foreign key (Veiculo_idVeiculo) references Veiculo(idVeiculo),
    foreign key (Avaliacao_idAvaliacao) references Avaliacao(idAvaliacao)
);

create table OS_Servico_Mecanico(
    OS_idOS INT,
    Servico_idServico INT,
    Mecanico_idMecanico INT,
    primary key(OS_idOS, Servico_idServico, Mecanico_idMecanico),
    foreign key (OS_idOS) references OS(idOS),
    foreign key (Servico_idServico) references Servico(idServico),
    foreign key (Mecanico_idMecanico) references Mecanico(idMecanico)
);

-- ======================
-- EXEMPLOS DE INSERT
-- ======================
insert into Cliente (Nome, Telefone, Endereco) values 
('Joao Silva', '11999999999', 'Rua A, 10'),
('Maria Santos', '11988888888', 'Rua B, 20');

insert into Veiculo (Ano, Placa, Modelo, Cliente_idCliente) values
(2018, 'ABC1234', 'Gol', 1),
(2020, 'XYZ9876', 'Onix', 2);

insert into Mecanico (Nome, Codigo, Endereco, Especialidade) values
('Carlos Pereira', 'M001', 'Rua C, 30', 'Motor'),
('Ana Lima', 'M002', 'Rua D, 40', 'Eletrica');

insert into Servico (Descricao, ValorMaoDeObra) values
('Troca de óleo', 150.00),
('Alinhamento', 120.00);

insert into Avaliacao (DataAvaliacao, Nota, Comentario) values
('2025-10-18', '5', 'Serviço excelente'),
('2025-10-19', '4', 'Bom atendimento');

insert into OS (DataEmissao, DataEntrega, ValorTotal, Status_, Veiculo_idVeiculo, Avaliacao_idAvaliacao) values
('2025-10-18', '2025-10-20', 250.00, 'Em andamento', 1, 1),
('2025-10-19', '2025-10-21', 300.00, 'Concluido', 2, 2);

insert into OS_Servico_Mecanico (OS_idOS, Servico_idServico, Mecanico_idMecanico) values
(1, 1, 1),
(1, 2, 2),
(2, 1, 2);

-- SELECT SIMPLES

select * from Cliente;
select * from Veiculo;
select * from Mecanico;
select * from Servico;
select * from Avaliacao;
select * from OS;
select * from OS_Servico_Mecanico;

-- FILTROS COM WHERE
-- ======================select * from Veiculo where Ano = 2018;
select * from OS where Status_ = 'Em andamento';
select * from Cliente where Nome like 'J%';

-- EXPRESSÕES DERIVADAS

select Nome, concat('Cliente: ', Nome) as NomeCompleto from Cliente;
select idOS, ValorTotal, ValorTotal * 0.9 as Valor_com_Desconto from OS;

-- ORDER BY

select * from OS order by ValorTotal desc;
select * from Cliente order by Nome asc;

-- HAVING

select Status_, avg(ValorTotal) as MediaValor from OS group by Status_ having avg(ValorTotal) > 200;

-- JOIN ENTRE TABELAS

-- Cliente e seus veículos
select c.Nome, v.Modelo, v.Placa 
from Cliente c 
join Veiculo v on v.Cliente_idCliente = c.idCliente;

-- OS, veículo e avaliação
select o.idOS, v.Modelo as Veiculo, a.Nota, a.Comentario 
from OS o
join Veiculo v on o.Veiculo_idVeiculo = v.idVeiculo
join Avaliacao a on o.Avaliacao_idAvaliacao = a.idAvaliacao;

-- OS, serviço e mecânico
select o.idOS, s.Descricao as Servico, m.Nome as Mecanico 
from OS_Servico_Mecanico osm
join OS o on osm.OS_idOS = o.idOS
join Servico s on osm.Servico_idServico = s.idServico
join Mecanico m on osm.Mecanico_idMecanico = m.idMecanico;

-- UPDATE

update Cliente set Endereco = 'Rua Nova, 123' where idCliente = 1;
update OS set Status_ = 'Concluido' where idOS = 1;
update Servico set ValorMaoDeObra = 179.90 where idServico = 1;

-- DADOS DEPOIS DE UPDATE

select * from Cliente;
select * from OS;
select * from OS_Servico_Mecanico;
select * from Servico;

-- DELETE

delete from OS_Servico_Mecanico where OS_idOS = 1 and Servico_idServico = 1 and Mecanico_idMecanico = 1;
delete from OS where idOS = 1;
delete from Cliente where idCliente = 1;

-- DADOS DEPOIS DE DELETE

select * from Cliente;
select * from OS;
select * from OS_Servico_Mecanico;
select * from Servico;
