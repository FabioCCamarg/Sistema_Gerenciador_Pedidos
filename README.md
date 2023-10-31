# Sistema_Gerenciador_Pedidos
## Atividade: Sistema de Gerenciamento de Pedidos
/*Objetivo: Criar um sistema de gerenciamento de pedidos em um banco de dados 
utilizando stored procedures, triggers, views e JOINs no MySQL Workbench.*/

-- Criando um banco de dados "gerenciadorpedidos".

    create database gerenciadorpedidos;
        
## Etapa 1: Criação de Tabelas e Inserção de Dados

/*Crie as tabelas "Clientes" e "Pedidos" com campos apropriados. 
Insira dados de exemplo nas tabelas para simular clientes e pedidos. 
Certifique-se de incluir uma chave primária em cada tabela.*/

-- Tabela clientes.

	create table clientes(
	id int auto_increment primary key,
	nome varchar(50),
	email varchar(50),
	telefone varchar(50),
	endereco varchar(50)
	);

-- inserindo dados a tabela clientes.

	insert into clientes (nome, email, telefone, endereco)values
	('Ricardo','ricardo@gmail.com', '(15)99331-2213','rua Tabajaré'),
	('Douglas','douglas@gmail.com','(15)98119-6654','rua Ganso'),
	('Mari', 'mari@gmail.com', '(15)98871-2332', 'rua das Nascentes');

-- criando tabela pedidos.

	create table pedidos(
	id int auto_increment primary key,
	data_pedido date,
	valor decimal(9,2),
	cliente_id int,
	foreign key (cliente_id) references clientes(id)
	);

-- inserindo dados a tabela pedidos.

	insert into pedidos (data_pedido, valor, cliente_id) values
	('2023-01-02', 300.00, 1),
	('2023-02-15',100.50, 2),
	('2023-09-08', 400.10, 3);

## Etapa 2: Criação de Stored Procedure

/*Crie uma stored procedure chamada "InserirPedido" que permite inserir um novo pedido na tabela "Pedidos" com as informações apropriadas.
 A stored procedure deve receber parâmetros como o ID do cliente e os detalhes do pedido. 
 Ao término teste o funcionamento da stored procedure criada inserindo um pedido.*/

	DELIMITER $$

	create procedure InserirPedido(
	in cliente_id int ,
	in data_pedido date,
	in valor decimal(9,2)
	)
	begin
		insert into pedidos(cliente_id, data_pedido, valor) values 
		(cliente_id, data_pedido, valor);
	end$$

	DELIMITER ;

	call InserirPedido(4,'2023-05-05',50.00);

	select * from pedidos;

## Etapa 3: Trigger

/* Crie uma trigger que seja acionada APÓS a inserção de um novo pedido na tabela "Pedidos". 
A trigger deve calcular o valor total dos pedidos para o cliente correspondente e atualizar um campo "TotalPedidos" na tabela "Clientes" 
com o valor total. Teste a Trigger inserindo um novo pedido na tabela "Pedidos“.*/

-- criando uma coluna na tabela cliente chamada "TotalPedidos".

    ALTER TABLE clientes ADD TotalPedidos DECIMAL(9, 2) DEFAULT 0;

-- criando a Trigger:

	DELIMITER $$
	create trigger AtualizaTotalPedidos 
	after insert on pedidos
	for each row
	begin
		update clientes
		set TotalPedidos = TotalPedidos + new.valor
		where id = new.cliente_id;
	end$$
	DELIMITER ;

-- inserindo daodos para teste da trigger:

	INSERT INTO Pedidos (cliente_id, data_pedido, valor)
	VALUES (2, '2023-11-11', 60.00);

select* from clientes;


## Etapa 4: View

/*Crie uma view chamada "PedidosClientes" que combina informações das tabelas "Clientes" e "Pedidos"
 usando JOIN para mostrar os detalhes dos pedidos e os nomes dos clientes.*/
 
    create view PedidosClientes as
    select 
    clientes.nome, 
    pedidos.valor, 
    pedidos.data_pedido, 
    pedidos.cliente_id
    from clientes
    inner join pedidos
    on clientes.id = pedidos.cliente_id;


## Etapa 5: Consulta com JOIN

-- Escreva uma consulta SQL que utiliza JOIN para listar os detalhes dos pedidos de cada cliente, 
-- incluindo o nome do cliente e o valor total de seus pedidos. Utilize a view "PedidosClientes" para essa consulta.

		select * from PedidosClientes;
  
-- Logo abaixo uma imagem de diagrama Lógico das tabelas.

![exer1](https://raw.githubusercontent.com/FabioCCamarg/Sistema_Gerenciador_Pedidos/main/ImagemSistemaGerenciadorPedidos.png)
