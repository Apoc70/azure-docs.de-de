---
title: "Prognose – ARIMA (Autoregressive Integrated Moving Average, autoregressiver integrierter gleitender Mittelwert) | Microsoft Docs"
description: "Prognose – ARIMA (Autoregressive Integrated Moving Average, autoregressiver integrierter gleitender Mittelwert)"
services: machine-learning
documentationcenter: 
author: yijichen
manager: jhubbard
editor: cgronlun
ms.assetid: 1e0d525f-8a9e-4b42-87e0-c9423f059f8c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: yijichen
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 22a44e1c47748a54950d9ad732a04c08bbcefd87


---
# <a name="forecasting---autoregressive-integrated-moving-average-arima"></a>Prognose – ARIMA (Autoregressive Integrated Moving Average, autoregressiver integrierter gleitender Mittelwert)
Dieser [Dienst](https://datamarket.azure.com/dataset/aml_labs/arima) implementiert ARIMA (Autoregressive Integrated Moving Average, autoregressiver integrierter gleitender Mittelwert), um Vorhersagen basierend auf den vom Benutzer bereitgestellten Verlaufsdaten zu erzeugen. Erhöht sich der Bedarf für ein bestimmtes Produkt in diesem Jahr? Kann ich meine Produktverkäufe für die Weihnachtssaison vorhersagen, damit ich meine Inventur effektiv planen kann? Planungsmodelle sind für solche Fragen die passende Lösung. Angesichts der letzten Daten, untersuchen diese Modelle versteckte Trends und Saisonabhängigkeit, um zukünftige Trends vorherzusagen. 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> Dieser Webdienst kann von Benutzern verwendet werden – beispielsweise über eine mobile App, eine Website oder sogar über einen lokalen Computer. Dieser Webdienst ist jedoch auch ein gutes Beispiel dafür, wie Azure Machine Learning zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann. Mit nur wenigen Codezeilen R-Code und einigen Klicks in Azure Machine Learning Studio können Sie ein Experiment mit R-Code erstellen und als Webdienst veröffentlichen. Der Webdienst kann dann im Azure Marketplace veröffentlicht und von Benutzern und Geräten auf der ganzen Welt genutzt werden – ohne Einrichtung einer Infrastruktur durch den Autor des Webdiensts.
> 
> 

## <a name="consumption-of-web-service"></a>Nutzung des Webdiensts
Dieser Dienst akzeptiert 4 Argumente und berechnet die ARIMA-Prognosen.
Die Eingabeargumente sind:

* Frequency – Gibt die Häufigkeit der Rohdaten an (täglich/wöchentlich/monatlich/vierteljährlich/jährlich)
* Horizon – Zeitrahmen der zukünftigen Prognose
* Date – Hinzufügen der neuen Zeitreihendaten für Zeit
* Value – Hinzufügen der neuen Zeitreihendaten für Datenwerte

Die Ausgabe des Dienstes sind die berechneten Werte für die Prognose. 

Eine Beispieleingabe wäre: 

* Frequency – 12
* Horizon – 12
* Date – 1/15/2012;2/15/2012;3/15/2012;4/15/2012;5/15/2012;6/15/2012;7/15/2012;8/15/2012;9/15/2012;10/15/2012;11/15/2012;12/15/2012; 1/15/2013;2/15/2013;3/15/2013;4/15/2013;5/15/2013;6/15/2013;7/15/2013;8/15/2013;9/15/2013;10/15/2013;11/15/2013;12/15/2013; 1/15/2014;2/15/2014;3/15/2014;4/15/2014;5/15/2014;6/15/2014;7/15/2014;8/15/2014;9/15/2014
* Value – 3.479;3.68;3.832;3.941;3.797;3.586;3.508;3.731;3.915;3.844;3.634;3.549;3.557;3.785;3.782;3.601;3.544;3.556;3.65;3.709;3.682;3.511; 3.429;3.51;3.523;3.525;3.626;3.695;3.711;3.711;3.693;3.571;3.509

> Dieser Dienst, der im Azure Marketplace gehostet wird, ist ein OData-Dienst. Diese Dienste können durch POST- oder GET-Methoden aufgerufen werden. 
> 
> 

Es gibt mehrere Möglichkeiten, den Dienst auf automatisierte Weise zu nutzen ([hier](http://microsoftazuremachinelearning.azurewebsites.net/ArimaForecasting.aspx) finden Sie eine Beispiel-App).

### <a name="starting-c-code-for-web-service-consumption"></a>Starten von C#-Code für Webdienstnutzung:
    public class Input
    {
        public string frequency;
        public string horizon;
        public string date;
        public string value;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
         byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
         return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }


    void Main()
    {
          var input = new Input() { frequency = TextBox1.Text, horizon = TextBox2.Text, date = TextBox3.Text, value = TextBox4.Text };
        var json = JsonConvert.SerializeObject(input);
        var acitionUri =  "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";

          var httpClient = new HttpClient();
           httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere","ChangeToAPIKey");
           httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
          var query = httpClient.PostAsync(acitionUri,new StringContent(json));
          var result = query.Result.Content;
          var scoreResult = result.ReadAsStringAsync().Result;
      }

## <a name="creation-of-web-service"></a>Erstellen des Webdiensts
> Dieser Webdienst wurde mithilfe von Azure Machine Learning erstellt. Eine kostenlose Testversion sowie Einführungsvideos zum Erstellen von Experimenten und [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md) finden Sie unter [azure.com/ml](http://azure.com/ml). Im Folgenden finden Sie einen Screenshot des Experiments, mit dem der Webdienst erstellt wurde und Beispielcode für die einzelnen Module im Experiment.
> 
> 

In Azure Machine Learning wurde ein neues leeres Experiment erstellt. Stichprobeneingabedaten wurden mit einem vordefinierten Datenschema hochgeladen. Mit dem Schema ist ein [Execute R Script][execute-r-script]-Modul verknüpft, welches das ARIMA-Prognosemodell mithilfe der auto.arima- und forecast-Funktionen von R generiert. 

### <a name="experiment-flow"></a>Experimentablauf:
![Arbeitsbereich erstellen][2]

#### <a name="module-1"></a>Modul 1:
    # Add in the CSV file with the data in the format shown below 
![Arbeitsbereich erstellen][3]    

#### <a name="module-2"></a>Modul 2:
    # data input
    data <- maml.mapInputPort(1) # class: data.frame
    library(forecast)

    # preprocessing
    colnames(data) <- c("frequency", "horizon", "dates", "values")
    dates <- strsplit(data$dates, ";")[[1]]
    values <- strsplit(data$values, ";")[[1]]

    dates <- as.Date(dates, format = '%m/%d/%Y')
    values <- as.numeric(values)

    # fit a time-series model
    train_ts<- ts(values, frequency=data$frequency)
    fit1 <- auto.arima(train_ts)
    train_model <- forecast(fit1, h = data$horizon)
    plot(train_model)

    # produce forecasting
    train_pred <- round(train_model$mean,2)
    data.forecast <- as.data.frame(t(train_pred))
    colnames(data.forecast) <- paste("Forecast", 1:data$horizon, sep="")

    # data output
    maml.mapOutputPort("data.forecast");


## <a name="limitations"></a>Einschränkungen
Dies ist ein sehr einfaches Beispiel für die Prognose mit ARIMA. Wie aus dem oben stehenden Beispielcode ersichtlich ist, wird kein Abfangen von Fehlern implementiert, und der Dienst geht davon aus, dass alle Variablen kontinuierliche/positive Werte sind und die Häufigkeit eine ganze Zahl größer als 1 sein sollte. Die Länge der Vektoren Datum und Wert sollte identisch sein. Die Variable für das Datum muss dem Format "mm/tt/jjjj" entsprechen.

## <a name="faq"></a>Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-arima/arima-img1.png
[2]: ./media/machine-learning-r-csharp-arima/arima-img2.png
[3]: ./media/machine-learning-r-csharp-arima/arima-img3.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/




<!--HONumber=Nov16_HO3-->


