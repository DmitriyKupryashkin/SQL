ТЕСТОВОЕ ЗАДАНИЕ №1
Требуется создать новую базу данных TEST_DEST, с идентичными таблицами, с одной оговоркой – в таблице AccessGroups значения первичного ключа должны начинаться со значения 1000 и генерироваться с шагом 50. Далее необходимо скопировать информацию о группах прав доступа и их командах, сохранив содержимое групп, а также содержимое таблицы Objects, также сохраняя права доступа.

ТЕСТОВОЕ ЗАДАНИЕ №2
После выполнения тестового задания №1 требуется исправить ситуацию с избыточностью групп прав доступа, т.е. заменить все идентичные по командам группы прав доступа, которые были созданы для объектов списка п.3, на первые подходящие группы. При этом требуется удалить лишние группы и их команды из таблиц, 
Далее необходимо преобразовать временные группы в постоянные (указав значение колонки Temporary равным 0 и задав какое-то название группы вместо пустой строки).

ПРИМЕЧАНИЕ: Перед отправкой результатов на проверку проверьте, выполняются ли ваши скрипты, соответствуют ли результаты работы ваших скриптов условиям заданий.


ПРИЛОЖЕНИЕ №1

/*    ==Параметры сценариев==
    Версия целевого сервера : SQL Server 2008 R2
    Выпуск целевого ядра СУБД : Выпуск Microsoft SQL Server Express Edition
    Тип целевого ядра СУБД : Изолированный SQL Server
*/

USE [master]
GO

SET NOCOUNT ON

-- !!! ВНИМАНИЕ !!!
-- !!! Исправить пути к создаваемым базам данных !!!

CREATE DATABASE [TESTS_SOURCE]
 ON  PRIMARY 
( NAME = N'TESTS_SOURCE', FILENAME = N'F:\Databases\Backups\TESTS_SOURCE.mdf' , SIZE = 1024KB , MAXSIZE = 1GB, FILEGROWTH = 1%)
 LOG ON 
( NAME = N'TESTS_SOURCE_log', FILENAME = N'F:\Databases\Backups\TESTS_SOURCE.ldf' , SIZE = 1024KB , MAXSIZE = 1GB, FILEGROWTH = 1%)
GO

CREATE DATABASE [TESTS_DEST]
 ON  PRIMARY 
( NAME = N'TESTS_DEST', FILENAME = N'F:\Databases\Backups\TESTS_DESTS.mdf' , SIZE = 1024KB , MAXSIZE = 1GB, FILEGROWTH = 1%)
 LOG ON 
( NAME = N'TESTS_DEST_log', FILENAME = N'F:\Databases\Backups\TESTS_DESTS.ldf' , SIZE = 1024KB , MAXSIZE = 1GB, FILEGROWTH = 1%)
GO


-- [TESTS_SOURCE].[AccessGroups]

USE [TESTS_SOURCE]
GO

CREATE TABLE [dbo].[AccessGroups](
	[PK] [int] IDENTITY(1,1) NOT NULL,
	[Guid] [uniqueidentifier] NOT NULL,
	[Name] [nvarchar](255) NOT NULL,
	[TypeID] [int] NOT NULL,
	[Temporary] [int] NOT NULL,
	[SystemType] [int] NOT NULL,
 CONSTRAINT [PK_AccessGroups] PRIMARY KEY CLUSTERED 
(
	[PK] ASC
) WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Guid]  DEFAULT (newid()) FOR [Guid]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Name]  DEFAULT ('') FOR [Name]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_TypeID]  DEFAULT ((0)) FOR [TypeID]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Temporary]  DEFAULT ((0)) FOR [Temporary]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_SystemType]  DEFAULT ((0)) FOR [SystemType]

CREATE NONCLUSTERED INDEX [IX_AccessGroups_TypeID] ON [dbo].[AccessGroups](
	[TypeID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE UNIQUE NONCLUSTERED INDEX [UK_AccessGroups_Guid] ON [dbo].[AccessGroups](
	[Guid] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
GO


-- [TESTS_SOURCE].[AccessGroupCommands]

USE [TESTS_SOURCE]
GO

CREATE TABLE [dbo].[AccessGroupCommands](
	[CommandID] [int] NOT NULL,
	[GroupID] [int] NOT NULL,
	[Enable] [int] NOT NULL,
 CONSTRAINT [PK_AccessGroupCommands] PRIMARY KEY CLUSTERED 
(
	[CommandID] ASC,
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_CommandID] ON [dbo].[AccessGroupCommands](
	[CommandID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_GroupID] ON [dbo].[AccessGroupCommands](
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_Optimize1] ON [dbo].[AccessGroupCommands](
	[CommandID] ASC,
	[GroupID] ASC,
	[Enable] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_CommandID]  DEFAULT ((0)) FOR [CommandID]
ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_GroupID]  DEFAULT ((0)) FOR [GroupID]
ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_Enable]  DEFAULT ((0)) FOR [Enable]
ALTER TABLE [dbo].[AccessGroupCommands]  WITH CHECK ADD  CONSTRAINT [FK_AccGrpCommands_AccGroups] FOREIGN KEY([GroupID]) REFERENCES [dbo].[AccessGroups] ([PK]) ON DELETE CASCADE
ALTER TABLE [dbo].[AccessGroupCommands] CHECK CONSTRAINT [FK_AccGrpCommands_AccGroups]
GO


-- [TESTS_SOURCE].[Objects]

USE [TESTS_SOURCE]
GO

CREATE TABLE [dbo].[Objects](
	[PK] [int] IDENTITY(1,1) NOT NULL,
	[ObjectID] [int] NOT NULL,
	[AccessGroupID] [int] NOT NULL,
	[Name] [nvarchar](255) NOT NULL,
	[Guid] [uniqueidentifier] NOT NULL,
 CONSTRAINT [PK_Objects] PRIMARY KEY CLUSTERED 
(
	[PK] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Objects] ADD  DEFAULT ((0)) FOR [ObjectID]
ALTER TABLE [dbo].[Objects] ADD  DEFAULT ((0)) FOR [AccessGroupID]
ALTER TABLE [dbo].[Objects] ADD  DEFAULT (N'') FOR [Name]
ALTER TABLE [dbo].[Objects] ADD  CONSTRAINT [DF_Objects_Guid]  DEFAULT (newid()) FOR [Guid]
ALTER TABLE [dbo].[Objects]  WITH CHECK ADD  CONSTRAINT [FK_Objects_AccGroups] FOREIGN KEY([AccessGroupID]) REFERENCES [dbo].[AccessGroups] ([PK]) ON DELETE SET DEFAULT ON UPDATE CASCADE
ALTER TABLE [dbo].[Objects] CHECK CONSTRAINT [FK_Objects_AccGroups]
GO


-- [TESTS_DEST].[AccessGroups]

USE [TESTS_DEST]
GO

CREATE TABLE [dbo].[AccessGroups](
	[PK] [int] IDENTITY(1000,50) NOT NULL,
	[Guid] [uniqueidentifier] NOT NULL,
	[Name] [nvarchar](255) NOT NULL,
	[TypeID] [int] NOT NULL,
	[Temporary] [int] NOT NULL,
	[SystemType] [int] NOT NULL,
 CONSTRAINT [PK_AccessGroups] PRIMARY KEY CLUSTERED 
(
	[PK] ASC
) WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Guid]  DEFAULT (newid()) FOR [Guid]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Name]  DEFAULT ('') FOR [Name]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_TypeID]  DEFAULT ((0)) FOR [TypeID]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_Temporary]  DEFAULT ((0)) FOR [Temporary]
ALTER TABLE [dbo].[AccessGroups] ADD  CONSTRAINT [DF_AccessGroups_SystemType]  DEFAULT ((0)) FOR [SystemType]

CREATE NONCLUSTERED INDEX [IX_AccessGroups_TypeID] ON [dbo].[AccessGroups](
	[TypeID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE UNIQUE NONCLUSTERED INDEX [UK_AccessGroups_Guid] ON [dbo].[AccessGroups](
	[Guid] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, IGNORE_DUP_KEY = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
GO

-- [TESTS_DEST].[AccessGroupCommands]

USE [TESTS_DEST]
GO

CREATE TABLE [dbo].[AccessGroupCommands](
	[CommandID] [int] NOT NULL,
	[GroupID] [int] NOT NULL,
	[Enable] [int] NOT NULL,
 CONSTRAINT [PK_AccessGroupCommands] PRIMARY KEY CLUSTERED 
(
	[CommandID] ASC,
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_CommandID] ON [dbo].[AccessGroupCommands](
	[CommandID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_GroupID] ON [dbo].[AccessGroupCommands](
	[GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

CREATE NONCLUSTERED INDEX [IX_AccGroupCommands_Optimize1] ON [dbo].[AccessGroupCommands](
	[CommandID] ASC,
	[GroupID] ASC,
	[Enable] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]

ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_CommandID]  DEFAULT ((0)) FOR [CommandID]
ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_GroupID]  DEFAULT ((0)) FOR [GroupID]
ALTER TABLE [dbo].[AccessGroupCommands] ADD  CONSTRAINT [DF_AccGrpCommands_Enable]  DEFAULT ((0)) FOR [Enable]
ALTER TABLE [dbo].[AccessGroupCommands]  WITH CHECK ADD  CONSTRAINT [FK_AccGrpCommands_AccGroups] FOREIGN KEY([GroupID]) REFERENCES [dbo].[AccessGroups] ([PK]) ON DELETE CASCADE
ALTER TABLE [dbo].[AccessGroupCommands] CHECK CONSTRAINT [FK_AccGrpCommands_AccGroups]
GO

-- [TESTS_DEST].[Objects]

USE [TESTS_DEST]
GO

CREATE TABLE [dbo].[Objects](
	[PK] [int] IDENTITY(1,1) NOT NULL,
	[ObjectID] [int] NOT NULL,
	[AccessGroupID] [int] NOT NULL,
	[Name] [nvarchar](255) NOT NULL,
	[Guid] [uniqueidentifier] NOT NULL,
 CONSTRAINT [PK_Objects] PRIMARY KEY CLUSTERED 
(
	[PK] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Objects] ADD  DEFAULT ((0)) FOR [ObjectID]
ALTER TABLE [dbo].[Objects] ADD  DEFAULT ((0)) FOR [AccessGroupID]
ALTER TABLE [dbo].[Objects] ADD  DEFAULT (N'') FOR [Name]
ALTER TABLE [dbo].[Objects] ADD  CONSTRAINT [DF_Objects_Guid]  DEFAULT (newid()) FOR [Guid]
ALTER TABLE [dbo].[Objects]  WITH CHECK ADD  CONSTRAINT [FK_Objects_AccGroups] FOREIGN KEY([AccessGroupID]) REFERENCES [dbo].[AccessGroups] ([PK]) ON DELETE SET DEFAULT ON UPDATE CASCADE
ALTER TABLE [dbo].[Objects] CHECK CONSTRAINT [FK_Objects_AccGroups]
GO


-- Данные

-- [TESTS_SOURCE].[AccessGroups]

USE [TESTS_SOURCE]
GO

SET IDENTITY_INSERT [dbo].[AccessGroups] ON 
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (1, N'595ed941-f532-4b44-bb9c-13d5a3f48d3c', N'Авторский', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (2, N'd8e18e69-266c-41de-95be-926a918584fa', N'Административный', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (3, N'5fc89e67-fdc6-4fd6-9dd3-928087f6c42c', N'Просмотр', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (4, N'39153a87-d5e4-45d5-be34-d5dae77c52ec', N'Редакторский', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (5, N'00ee8f37-9a89-4efc-9997-adf0dc19927e', N'Авторский', 1, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (6, N'3df02e06-d36f-42ef-b532-23124ed3d578', N'Административный', 1, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (7, N'dbf749d3-4408-44ae-b93f-f8c105c1b543', N'Просмотр', 1, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (8, N'9e03b870-2bd7-4c83-9391-5057aff37c23', N'Редакторский', 1, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (9, N'43717784-2c09-4b24-80a1-d49a5b4c1bd3', N'Административный', 3, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (10, N'de59759e-7adc-45f0-8da0-35358b67e174', N'Полный', 4, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (11, N'9e53b028-b743-4a12-8c79-74e9b087d06a', N'Подключение', 4, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (12, N'7cac83b0-93e8-46f5-8a52-144d7d72a16f', N'Просмотр', 4, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (13, N'5f5f66ed-7c20-4fe4-9cb9-04faf6b40983', N'Запрет на редактирование', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (14, N'340acebb-1afd-404f-b6be-02bf29606dbc', N'Без ограничений', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (15, N'0841691d-24b2-4003-94a6-d86ffe87867a', N'Пользовательский', 3, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (16, N'efea5cc4-def7-43bb-ba73-4ff9849f6995', N'Просмотр и создание', 2, 0, 2)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (17, N'74a2c8f5-4c00-4df5-961e-2ad6336bcba7', N'Доступ "Только чтение + печать + копирование"', 2, 0, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (18, N'a21eec4b-9324-4d88-a52d-ea5b243016ba', N'Доступ "Просмотр + изменение + права доступа"', 2, 0, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (19, N'37f0c947-7f6a-491e-b13b-ffa8f8cffb4c', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (20, N'04ffdbc3-e831-426f-b940-2530eed3c5c2', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (21, N'149ccbe4-280c-4f85-b623-46e0ce9e59b4', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (22, N'a94b133f-e0fd-43a0-bea5-46258f052b81', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (23, N'936e1102-bd4d-4706-a46b-a7b0438ea12d', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (24, N'136e1102-bd4d-4706-a46b-a7b0438ea12d', N'', 2, 1, 0)
INSERT [dbo].[AccessGroups] ([PK], [Guid], [Name], [TypeID], [Temporary], [SystemType]) VALUES (25, N'236e1102-bd4d-4706-a46b-a7b0438ea12d', N'', 2, 1, 0)
SET IDENTITY_INSERT [dbo].[AccessGroups] OFF
GO

-- [TESTS_SOURCE].[AccessGroupCommands]

USE [TESTS_SOURCE]
GO

INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 3, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 16, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 17, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 18, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 19, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 20, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 21, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 22, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (1, 23, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (2, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (2, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (2, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (2, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 18, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 22, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (3, 23, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (4, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (4, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (4, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (4, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (4, 16, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 3, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 16, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 17, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 19, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 18, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 20, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 21, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 22, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 23, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 17, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 19, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 20, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 21, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (8, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (8, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (8, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (9, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (9, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (9, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (11, 5, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (11, 6, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (11, 8, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (12, 5, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (12, 6, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (13, 5, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (13, 6, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (14, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (15, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (15, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (15, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (15, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (16, 6, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (18, 5, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (18, 6, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (18, 7, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (18, 8, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (19, 10, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (19, 11, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (19, 12, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (20, 10, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (20, 11, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (21, 10, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (22, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (22, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (22, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (23, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (24, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (24, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (25, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (26, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (26, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (27, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (28, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (28, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (29, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (30, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (30, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (31, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (32, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (32, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (33, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (33, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (33, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (34, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (36, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (37, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (38, 1, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (38, 2, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (38, 4, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (38, 13, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (39, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (40, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (40, 15, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (41, 9, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 24, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 24, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 24, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (8, 24, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (5, 25, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (6, 25, 0)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (7, 25, 1)
INSERT [dbo].[AccessGroupCommands] ([CommandID], [GroupID], [Enable]) VALUES (8, 25, 1)

GO

-- [TESTS_SOURCE].[AccessGroupCommands]

USE [TESTS_SOURCE]
GO

INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (1, 19, 'Объект 001')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (2, 20, 'Объект 002')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (3, 21, 'Объект 003')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (4, 22, 'Объект 004')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (5, 23, 'Объект 005')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (5, 24, 'Объект 006')
INSERT [dbo].[Objects] ([ObjectID], [AccessGroupID], [Name]) VALUES (5, 25, 'Объект 007')
GO
