---
title: Video aplikace SaaS SQL
description: Tento článek indexuje různé časové body v našem 81minutovém videu o návrhu aplikace SaaS DB z konference Ignite, která se konala 11. Můžete přeskočit dopředu k části, která vás zajímá. Jsou popsány nejméně 3 vzory. Jsou popsány funkce Azure, které zjednodušují vývoj a správu.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.topic: conceptual
ms.openlocfilehash: 1ee8f2fff958045f652b72358ab928f82920fd6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067614"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Video indexované a anotované pro víceklientské aplikace SaaS pomocí Azure SQL Database

Tento článek je anotovaný index do časových umístění 81 minut video o saas nájemní modely nebo vzory. Tento článek umožňuje přeskočit zpět nebo vpřed ve videu, na které části vás zajímá. Video vysvětluje hlavní možnosti návrhu pro databázovou aplikaci s více klienty v Azure SQL Database. Video obsahuje ukázky, návody kódu pro správu a někdy více podrobností informovaných zkušenostmi, než může být v naší písemné dokumentaci.

Video zesiluje informace v naší písemné dokumentaci, která je k dispozici na adrese: 
- *Koncepční:* [Víceklientské vzory nájmu databáze SaaS][saas-concept-design-patterns-563e]
- *Návody:* [Wingtip Vstupenky SaaS aplikace][saas-how-welcome-wingtip-app-679t]

Video a články popisují mnoho fází vytváření víceklientské aplikace v Azure SQL Database v cloudu. Speciální funkce Azure SQL Database usnadňují vývoj a implementaci víceklientských aplikací, které se snadněji spravují a spolehlivě spravovají.

Pravidelně aktualizujeme naši písemnou dokumentaci. Video není upraveno ani aktualizováno, takže nakonec může být více jeho detailů zastaralé.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Posloupnost 38 časově indexovaných snímků obrazovky

Tato část indexuje časové umístění pro 38 diskusí v průběhu 81 minut video. Pokaždé, když index je anotován s screenshot z videa, a někdy s dalšími informacemi.

Každý časový index je ve formátu *h:mm:ss*. Například druhé indexované časové umístění s názvem **Cíle relace**začíná v přibližném časovém umístění **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompaktní odkazy na umístění času indexované videem

Následující názvy jsou odkazy na jejich odpovídající anotované oddíly dále v tomto článku:

- [1. **(Start)** Uvítací snímek, 0:00:03](#anchor-image-wtip-min00001)
- [2. Cíle zasedání, 0:03:11](#anchor-image-wtip-min00311)
- [3. Program jednání, 0:04:17](#anchor-image-wtip-min00417)
- [4. Víceklientské webové aplikace, 0:05:05](#anchor-image-wtip-min00505)
- [5. Webový formulář aplikace v akci, 0:05:55](#anchor-image-wtip-min00555)
- [6. Náklady na klienta (škálování, izolace, obnovení), 0:09:31](#anchor-image-wtip-min00931)
- [7. Databázové modely pro víceklientů: klady a zápory, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybridní model kombinuje výhody MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Single-tenant vs multi-tenant: klady a zápory, 0:16:44](#anchor-image-wtip-min01644)
- [10. Fondy jsou nákladově efektivní pro nepředvídatelné úlohy, 0:19:36](#anchor-image-wtip-min01936)
- [11. Ukázka databáze na klienta a hybridní ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Formulář živé aplikace zobrazující Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB a ne DBA v nedohlednu, 0:28:54](#anchor-image-wtip-min02854)
- [14. MYOB elastické použití bazénu příklad, 0:29:40](#anchor-image-wtip-min02940)
- [15. Učení se od MYOB a dalších isv, 0:31:36](#anchor-image-wtip-min03136)
- [16. Vzory skládat do E2E SaaS scénář, 0:43:15](#anchor-image-wtip-min04315)
- [17. Kanonická hybridní víceklientská aplikace SaaS, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS ukázková aplikace, 0:48:10](#anchor-image-wtip-min04810)
- [19. Scénáře a vzory prozkoumány v seminářích, 0:49:10](#anchor-image-wtip-min04910)
- [20. Ukázka výukových programů a úložiště GitHub, 0:50:18](#anchor-image-wtip-min05018)
- [21. Úložiště GitHub Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Zkoumání vzorů, 0:56:20](#anchor-image-wtip-min05620)
- [23. Zřizování nájemníků a registrace, 0:57:44](#anchor-image-wtip-min05744)
- [24. Zřizování klientů a připojení k aplikacím, 0:58:58](#anchor-image-wtip-min05858)
- [25. Ukázka skriptů pro správu zřizování jednoho klienta, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell do zřízení a katalogu, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * Od tenantůExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Správa nepředvídatelné úlohy klienta, 1:04:36](#anchor-image-wtip-min10436)
- [29. Monitorování elastického bazénu, 1:06:39](#anchor-image-wtip-min10639)
- [30. Generování zatížení a sledování výkonu, 1:09:42](#anchor-image-wtip-min10942)
- [31. Správa schémat ve velkém měřítku, 1:10:33](#anchor-image-wtip-min11033)
- [32. Distribuovaný dotaz mezi databázemi klientů, 1:12:21](#anchor-image-wtip-min11221)
- [33. Ukázka generování vstupenek, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS adhoc analytics, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrahovat data klienta do SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Graf denního prodeje distribuce, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zabalte a výzva k akci, 1:19:52](#anchor-image-wtip-min11952)
- [38. Zdroje pro více informací, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Umístění času anotovaného indexu ve videu

Kliknutím na libovolný snímek obrazovky se dostanete na přesné místo ve videu.


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Uvítací snímek, 0:00:01

*Učení se z MYOB: Návrhové vzory pro aplikace SaaS v Azure SQL Database - BRK3120*

[![Uvítací snímek][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Název: Učení se od MYOB: Návrhové vzory pro aplikace SaaS v Azure SQL Database
- Bill.Gibson@microsoft.com
- Hlavní správce programů, Azure SQL Database
- Microsoft Ignite session BRK3120, Orlando, FL USA, říjen/11 2017


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Cíle zasedání, 0:01:53
[![Cíle relace][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternativní modely pro víceklientské aplikace s klady a zápory.
- SaaS vzory ke snížení nákladů na vývoj, správu a zdroje.
- Ukázková aplikace + skripty.
- Funkce PaaS + vzory SaaS dělají z databáze SQL vysoce škálovatelnou a nákladově efektivní datovou platformu pro víceklientské SaaS.


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Program jednání, 0:04:09
[![Agendy][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Víceklientské webové aplikace, 0:05:00
[![Aplikace Wingtip SaaS: Webová aplikace pro více klientů][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. Webový formulář aplikace v akci, 0:05:39
[![Webový formulář aplikace v akci][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Náklady na klienta (škálování, izolace, obnovení), 0:06:58
[![Náklady na klienta, škálování, izolace, obnovení][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Databázové modely pro víceklientů: klady a zápory, 0:09:52
[![Databázové modely pro víceklientské: výhody a nevýhody][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybridní model kombinuje výhody MT/ST, 0:12:29
[![Hybridní model kombinuje výhody MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Single-tenant vs multi-tenant: klady a zápory, 0:13:11
[![Single-tenant vs multi-tenant: klady a zápory][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Fondy jsou nákladově efektivní pro nepředvídatelné úlohy, 0:17:49
[![Fondy jsou nákladově efektivní pro nepředvídatelné úlohy][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Ukázka databáze na tenanta a hybridní ST/MT, 0:19:59
[![Ukázka databáze na klienta a hybridní ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Formulář živé aplikace zobrazující Dojo, 0:20:10
[![Formulář živé aplikace zobrazující Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB a ne DBA v nedohlednu, 0:25:06
[![MYOB a ne DBA v nedohlednu][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. MYOB elastické použití bazénu příklad, 0:29:30
[![PŘÍKLAD použití elastického fondu MYOB][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Učení se od MYOB a dalších isv, 0:31:25
[![Učení se od MYOB a dalších isv][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Vzory skládat do E2E SaaS scénář, 0:31:42
[![Vzory tvoří do scénáře E2E SaaS][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Kanonická hybridní víceklientská aplikace SaaS, 0:46:04
[![Kanonické hybridní víceklientské aplikace SaaS][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS ukázková aplikace, 0:48:01
[![Ukázková aplikace Wingtip SaaS][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. Scénáře a vzory prozkoumány v seminářích, 0:49:00
[![Scénáře a vzory prozkoumány v kurzech][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Ukázka výukových programů a úložiště GitHub, 0:50:12
[![Ukázkové kurzy a úložiště GitHub][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub repo Microsoft/WingtipSaaS, 0:50:32
[![GitHub repo Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Zkoumání vzorů, 0:56:15
[![Zkoumání vzorů][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Zřizování nájemníků a onboarding, 0:56:19
[![Zřizování nájemníků a registrace][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Zřizování klientů a připojení k aplikacím, 0:57:52
[![Zřizování klientů a připojení aplikací][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Ukázka skriptů pro správu zřizování jednoho klienta, 0:59:36
[![Ukázka zřizování skriptů pro správu jednoho klienta][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell do zřízení a katalogu, 0:59:56
[![PowerShell pro zřízení a katalog][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * Od tenantůExtended, 1:03:25
[![T-SQL SELECT * Z tenantůExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Správa nepředvídatelné úlohy klienta, 1:03:34
[![Správa nepředvídatelných úloh klienta][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Monitorování elastického bazénu, 1:06:32
[![Monitorování elastického bazénu][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Generování zatížení a sledování výkonu, 1:09:37
[![Generování zatížení a monitorování výkonu][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Správa schémat ve velkém měřítku, 1:09:40
[![Správa schémat ve velkém měřítku][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Distribuovaný dotaz mezi databázemi klientů, 1:11:18
[![Distribuovaný dotaz v databázích klientů][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Ukázka generování vstupenek, 1:12:28
[![Ukázka generování vstupenek][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS adhoc analytics, 1:12:35
[![Adhoc analýza SSMS][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrahovat data klienta do SQL DW, 1:15:46
[![Extrahovat data klienta do SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Graf denního prodeje distribuce, 1:16:38
[![Graf denní distribuce prodeje][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zabalte a výzva k akci, 1:17:43
[![Zabalte a výzvu k akci][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Zdroje pro více informací, 1:20:35
[![Zdroje pro více informací][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Příspěvek na blogu, květen 22, 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Koncepční:* [Víceklientské vzory nájmu databáze SaaS][saas-concept-design-patterns-563e]

- *Návody:* [Wingtip Vstupenky SaaS aplikace][saas-how-welcome-wingtip-app-679t]

- GitHub repozitáře pro příchutě Wingtip Vstupenky SaaS aplikace k pronájmu:
    - [Úložiště GitHub pro - samostatný aplikační model][github-wingtip-standaloneapp].
    - [Úložiště GitHub pro - DB na model tenanta][github-wingtip-dbpertenant].
    - [Úložiště GitHub pro - víceklientské DB model][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Další kroky

- [První výukový článek][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Uvítací snímek"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Cíle relace."

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agendy."

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Aplikace Wingtip SaaS: Webová aplikace pro více klientů"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "Webový formulář aplikace v akci"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Náklady na klienta, škálování, izolace, obnovení"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Databázové modely pro víceklientské: výhody a nevýhody"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridní model kombinuje výhody MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Single-tenant vs multi-tenant: klady a zápory"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Fondy jsou nákladově efektivní pro nepředvídatelné úlohy"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Ukázka databáze na klienta a hybridní ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Formulář živé aplikace zobrazující Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB a ne DBA v nedohlednu"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "PŘÍKLAD použití elastického fondu MYOB"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Učení se od MYOB a dalších isv"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Vzory tvoří do scénáře E2E SaaS"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Kanonické hybridní víceklientské aplikace SaaS"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Ukázková aplikace Wingtip SaaS"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "Scénáře a vzory prozkoumány v kurzech"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Ukázka výukových programů a úložiště GitHub"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub repo Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Zkoumání vzorů"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Zřizování nájemníků a registrace"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Zřizování klientů a připojení aplikací"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Ukázka zřizování skriptů pro správu jednoho klienta"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell pro zřízení a katalog"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * Z tenantůExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Správa nepředvídatelných úloh klienta"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Monitorování elastického bazénu"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Generování zatížení a monitorování výkonu"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Správa schémat ve velkém měřítku"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Distribuovaný dotaz v databázích klientů"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Ukázka generování vstupenek"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "Adhoc analýza SSMS"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrahovat data klienta do SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Graf denní distribuce prodeje"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zabalte a výzvu k akci"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Zdroje pro více informací"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

