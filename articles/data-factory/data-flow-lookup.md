---
title: Suchtransformation in einem Zuordnungsdatenfluss
description: Verweisen Sie mithilfe der Suchtransformation in einem Zuordnungsdatenfluss auf Daten aus einer anderen Quelle.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 78c6c1363af011a90865770d88c0037e50e958c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240413"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Suchtransformation in einem Zuordnungsdatenfluss

Verwenden Sie die Suchtransformation, um auf Daten aus einer anderen Quelle in einem Datenfluss zu verweisen. Die Suchtransformation fügt Spalten aus übereinstimmenden Daten an Ihre Quelldaten an.

Eine Suchtransformation ist mit einer linken äußeren Verknüpfung vergleichbar. Alle Zeilen aus dem primären Datenstrom sind im Ausgabedatenstrom mit zusätzlichen Spalten aus dem Suchdatenstrom enthalten. 

## <a name="configuration"></a>Konfiguration

![Suchtransformation](media/data-flow/lookup1.png "Nachschlagen")

**Primärer Datenstrom:** Der eingehende Datenstrom. Dieser Datenstrom entspricht der linken Seite einer Verknüpfung.

**Suchdatenstrom:** Die Daten, die an den primären Datenstrom angefügt werden. Welche Daten hinzugefügt werden, wird durch die Suchbedingungen festgelegt. Dieser Datenstrom entspricht der rechten Seite einer Verknüpfung.

**Übereinstimmung mit mehreren Zeilen:** Bei Aktivierung dieser Option werden für eine Zeile mit mehreren Übereinstimmungen im primären Datenstrom mehrere Zeilen zurückgegeben. Anderenfalls wird basierend auf der Bedingung „Übereinstimmung mit“ nur eine einzelne Zeile zurückgegeben.

**Übereinstimmung mit:** Nur sichtbar, wenn „Übereinstimmung mit mehreren Zeilen“ aktiviert ist. Wählen Sie aus, ob eine Übereinstimmung für eine beliebige Zeile, die erste Übereinstimmung oder die letzte Übereinstimmung ermittelt werden soll. Da die Ausführung bei Auswahl einer beliebigen Zeile am schnellsten ist, wird diese Einstellung empfohlen. Wenn die erste Zeile oder die letzte Zeile ausgewählt wird, müssen Sortierbedingungen angegeben werden.

**Suchbedingungen:** Wählen Sie aus, für welche Spalten eine Übereinstimmung ermittelt werden soll. Wenn die Gleichheitsbedingung erfüllt ist, werden die Zeilen als Übereinstimmung betrachtet. Zeigen Sie mit dem Mauszeiger auf „Berechnete Spalte“, und wählen Sie diese Option aus, um mithilfe der [Ausdruckssprache für Datenflüsse](data-flow-expression-functions.md) einen Wert zu extrahieren.

Die Suchtransformation unterstützt ausschließlich Gleichheitsübereinstimmungen. Um den Suchausdruck so anzupassen, dass er andere Operatoren (z. B. „größer als“) umfasst, sollten Sie einen [Cross Join in der Join-Transformation](data-flow-join.md#custom-cross-join) verwenden. Durch einen Cross Join werden mögliche kartesische Produktfehler bei der Ausführung vermieden.

In den Ausgabedaten sind alle Spalten beider Datenströme enthalten. Um doppelte oder unerwünschte Spalten zu löschen, fügen Sie nach Ihrer Suchtransformation eine [Auswahltransformation](data-flow-select.md) hinzu. Spalten können auch in einer Senkentransformation gelöscht oder umbenannt werden.

## <a name="analyzing-matched-rows"></a>Analysieren übereinstimmender Zeilen

Nach der Suchtransformation können Sie mithilfe der Funktion `isMatch()` feststellen, ob bei der Suche Übereinstimmungen für einzelne Zeilen ermittelt wurden.

![Suche: Muster](media/data-flow/lookup111.png "Suche: Muster")

Ein Beispiel für dieses Muster ist die Verwendung der Transformation für bedingtes Teilen für die Funktion `isMatch()`. Im obigen Beispiel durchlaufen übereinstimmende Zeilen den obersten Stream und nicht übereinstimmende Zeilen den Stream ```NoMatch```.

## <a name="testing-lookup-conditions"></a>Testen von Suchbedingungen

Verwenden Sie einen kleinen Satz bekannter Daten, wenn Sie die Suchtransformation mit Datenvorschau im Debugmodus testen. Bei der Stichprobenentnahme aus einem umfangreichen Dataset können Sie nicht vorhersagen, welche Zeilen und Schlüssel zu Testzwecken gelesen werden. Das Ergebnis ist nicht deterministisch. Das bedeutet, dass für Ihre Verknüpfungsbedingungen möglicherweise keine Übereinstimmungen zurückgegeben werden.

## <a name="broadcast-optimization"></a>Broadcastoptimierung

In Azure Data Factory werden Zuordnungsdatenflüsse in aufskalierten Spark-Umgebungen ausgeführt. Wenn Ihr Dataset in den Workerknotenspeicher eingepasst werden kann, kann Ihre Suchleistung durch die Aktivierung des Broadcastings optimiert werden.

![Broadcastjoin](media/data-flow/broadcast.png "Broadcastjoin")

Bei aktiviertem Broadcasting wird das gesamte Dataset in den Arbeitsspeicher verschoben. Bei kleineren Datasets, die nur wenige Tausend Zeilen enthalten, kann die Suchleistung durch das Broadcasting erheblich verbessert werden. Bei großen Datasets kann diese Option zu einer Ausnahme aufgrund von nicht genügend Arbeitsspeicher führen.

## <a name="data-flow-script"></a>Datenflussskript

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Beispiel

![Suchtransformation](media/data-flow/lookup-dsl-example.png "Nachschlagen")

Der nachfolgende Codeausschnitt zeigt das Datenflussskript für die obige Suchkonfiguration.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Nächste Schritte

* Für die [Join](data-flow-join.md)- und [Exists](data-flow-exists.md)-Transformation können mehrere Datenströme als Eingabe verwendet werden.
* Verwenden Sie eine [Transformation für bedingtes Teilen](data-flow-conditional-split.md) mit ```isMatch()```, um Zeilen nach übereinstimmenden und nicht übereinstimmenden Werten aufzuteilen.
