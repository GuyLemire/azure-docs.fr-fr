---
title: Bibliothèques de connexions pour SQL Database | Microsoft Docs
description: Fournit des liens de téléchargement de modules qui permettent de se connecter à SQL Server et SQL Database à partir d’un grand nombre de langages de programmation côté client.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 874aa2610ae1785e9f40841ed3c3279aac42d660
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321821"
---
# <a name="connectivity-libraries-and-frameworks-for-sql-server"></a>Bibliothèques et frameworks de connectivité pour SQL Server

Découvrez nos [didacticiels de démarrage](https://aka.ms/sqldev) pour commencer rapidement à utiliser des langages de programmation comme C#, Java, Node.js, PHP et Python. Ensuite, créez une application à l’aide de SQL Server sur Linux, Windows ou Docker sur macOS.

Le tableau suivant répertorie les bibliothèques de connectivité ou *pilotes* que les applications clientes peuvent utiliser dans une variété de langages pour se connecter à SQL Server et l’utiliser localement ou dans le cloud. Vous pouvez les utiliser sur Linux, Windows ou Docker, pour vous connecter à Azure SQL Database et Azure SQL Data Warehouse. 

| Langage | Plateforme | Ressources supplémentaires | Download | Prise en main |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET pour SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Télécharger](https://www.microsoft.com/net/download/) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Pilote Microsoft JDBC pour SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Télécharger](https://go.microsoft.com/fwlink/?linkid=852460) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Pilote PHP SQL pour SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Télécharger](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Pilote Node.js pour SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installer](https://msdn.microsoft.com/library/mt652094.aspx) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Pilote Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Choix d’installation : <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \*[pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Pilote Ruby pour SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installer](https://msdn.microsoft.com/library/mt711041.aspx) | [Prise en main](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Pilote Microsoft ODBC pour SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Télécharger](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

Le tableau suivant répertorie des exemples de frameworks de mappage relationnel objet (ORM) et de frameworks web que les applications clientes peuvent utiliser avec SQL Server exécuté localement ou dans le cloud. Vous pouvez utiliser les frameworks sur Linux, Windows ou Docker, pour vous connecter à SQL Database et SQL Data Warehouse. 

| Langage | Plateforme | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="related-links"></a>Liens connexes
- [Pilotes SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) utilisés pour se connecter à partir d’applications clientes
- Se connecter à SQL Database :
    - [Connexion à SQL Database à l’aide de .NET (C#)](sql-database-connect-query-dotnet.md)
    - [Connexion à SQL Database à l’aide de PHP sur Windows](sql-database-connect-query-php.md)
    - [Connexion à SQL Database à l’aide de Node.js](sql-database-connect-query-nodejs.md)
    - [Connexion à la base de données SQL à l’aide de Java avec JDBC sous Windows](sql-database-connect-query-java.md)
    - [Connexion à SQL Database à l’aide de Python](sql-database-connect-query-python.md)
    - [Connexion à SQL Database à l’aide de Ruby](sql-database-connect-query-ruby.md)
- Exemples de code de logique de nouvelle tentative :
    - [Connexion résiliente à SQL avec ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
    - [Connexion résiliente à SQL avec PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

