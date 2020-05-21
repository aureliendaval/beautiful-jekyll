---
layout: post
title: Programmer un questionnaire avec R
subtitle: Concevoir une étude de marché de A à Z avec R
gh-badge: [star, fork, follow]
tags: [Programmer, Questionnaire, Etude de marché]
comments: true
---

# Programmation du questionnaire {#questionnaire}

Vous sortez d'un atelier de travail avec l'équipe en charge du projet. Au préalable de cette réunion, votre directeur avait envoyé un premier draft du questionnaire. Cette réunion a permis de le finaliser et d'obtenir la validation du client. Il faut maintenant programmer le questionnaire. 

R n'est pas un outil de sondage comme [Survey Monkey](https://fr.surveymonkey.com/) ou [Askia](https://www.askia.com/). Cependant, avec le package [Shiny](https://shiny.rstudio.com/) (qui permet de construire des pages Web interactives sans HTML/CSS/JavaScript), il est possible de programmer une application de sondage qui permet d'afficher les différentes questions et de collecter les réponses. Sur le site [d'Askia](https://www.askia.com/), vous pouvez répondre à leur questionnaire de démonstration, ce qui vous montre dès à présent vers quoi nous souhaitons tendre. 

## Introduction au package Shiny 

Le questionnaire va donc être programmé via le package Shiny. Avant de programmer notre questionnaire, je vous propose de regarder comment ce package fonctionne. 

```{r, message=FALSE, warning=FALSE}

library(shiny)

```

Une application Shiny se compose de deux éléments : 

* L'Interface Utilisateur (UI) : vous allez gérer ici l'apparence de votre application, sa mise en page. 
* Le serveur (Server) : c'est les instructions qui régissent les interactions à l'intérieur de votre application. Qu'est ce qui se passe quand j'appuie sur ce bouton ? Qu'est ce qui doit être affiché ? 

Pour créer votre première application, il faut (dans R Studio) cliquer sur : Fichier > Nouveau Fichier > Shiny Web App > Un seul fichier > Nom de l'application : Questionnaire. Félicitations. Vous auriez pu créer deux fichiers (ui.R et server.R) en cliquant sur l'option "Fichiers Multiples". Pour ma part, je trouve qu'il est plus facile de suivre un tutoriel avec un seul fichier (app.R) pour reproduire facilement ce qui est montré. Cependant, je vous conseille lors de la programmation d'une application (où le nombre de lignes de code augmente au fil du développement) de diviser votre application en plusieurs fichiers pour vous y retrouver plus facilement.

Ci-dessous la structure d'une application Shiny :

```{r, message=FALSE, warning=FALSE, eval=FALSE}

# Chargement du package Shiny. 
library(shiny)

# L'Interface Utilisateur --> la page web de l'application. 
ui <- fluidPage()
   
# Le Serveur  --> les instructions pour les interactions. 
server <- function(input, output) {}

# Lancer l'application 
shinyApp(ui = ui, server = server)

```

Pour fonctionner, une application Shiny a besoin de : 

* Inputs : 

C'est ce qui permet à l'utilisateur d'intéragir avec votre application en modifiant certaines valeurs. Les inputs sont toujours à l'intérieur de la partie UI. Plusieurs type d'inputs (textInput, dateInput, numericInput, ...) sont possibles. Vous avez un aperçu [ici](https://shiny.rstudio.com/gallery/widget-gallery.html).

A l'intérieur de chaque Inputs, il y a plusieurs arguments. Deux sont communs à tous (InputID et Label). L'ID permet de mettre en place les interactions dans le Serveur et de savoir de quels Inputs on parle. L'ID est unique. Le label permet d'ajouter du texte avant l'Input dans votre Interface Utilisateur. Et enfin, d'autres arguments sonnt nécessaires en fonction de chaque Inputs. 

Ci-dessous un Input numérique. L'ID --> num. Le Label --> "Voici un Input numérique" (le h6, c'est pour signifier à l'application qu'il s'agit d'un titre taille 6). Pour finir, on peut ajouter une valeur de départ à cette Input : le chiffre affiché au départ sera le 1. 

```{r, message=FALSE, warning=FALSE}

numericInput(inputId = "num", label = h6("Voici un Input numérique"), value = 1)

```

<br/>

* Outputs : 

C'est ce qui permet d'afficher des graphiques, des espaces de textes ou des tableaux. Pour créer un Output, il faut : 
1. Définir où l'Ouput doit se situer dans l'application (UI)
2. Définir ce que cet Output doit afficher dans la partie Serveur.
Vous avez un aperçu [ici](https://shiny.rstudio.com/tutorial/written-tutorial/lesson4/) des différents Outputs possibles. 

Reprenons l'exemple ci-dessus avec un Input numérique. Et ajoutons lui un output : je souhaite que l'application ajoute un zone de texte avec le chiffre affiché dans l'Input, chiffre auquel je vais ajouter +2. Dans la partie UI, je notifie à l'application là où doit se trouver l'Output (textOutput --> ID: num2). Ensuite, dans la partie serveur, je vais dire à l'application ce qu'il doit afficher. Il doit afficher du texte (renderText). Ce texte, c'est la valeur de l'Input numérique renseigné par l'utilisateur +2. 

```{r, message=FALSE, warning=FALSE, fig.height=1}

# Chargement du package Shiny. 
library(shiny)

# L'Interface Utilisateur --> la page web de l'application. 
ui <- fluidPage(
  numericInput("num", label = h6("Voici un Input numérique"), value = 1), 
  p("Le chiffre ci dessus +2 :"),
  textOutput("num2")
)
   
# Le Serveur  --> les instructions pour les interactions. 
server <- function(input, output) {
  output$num2 <- renderText({input$num + 2})
}

# Lancer l'application 
shinyApp(ui = ui, server = server)

```

Pour clore cette introduction au package Shiny, je vous propose de créer une application "Calculatrice". Je me suis inspiré [ici](https://rstudio-pubs-static.s3.amazonaws.com/317038_62ec6c4336ec4b359103825f3dbe9b00.html#1) pour créer notre application.

3 Inputs : le premier chiffre, le second chiffre et une dernière entrée pour le choix de l'opérateur (+,-,/,x). L'Ouput : le résultat de ce calcul. 

```{r, message=FALSE, warning=FALSE, fig.height=1}

# Chargement du package Shiny. 
library(shiny)

# L'Interface Utilisateur --> la page web de l'application. 
ui <- fluidPage(
  
  titlePanel("Calculatrice"),
  
  sidebarPanel(
    numericInput("num1", "1er Nombre", 0),
    numericInput("num2", "2nd Nombre", 0),
    selectInput("operator", "L'opérateur",
                choices = c("+","-","x", "/"))),
  mainPanel(
    h2("Résultat:"),
    textOutput("output"))
  
)

# Le Serveur  --> les instructions pour les interactions. 
server <- function(input, output) {
  
  output$output <- renderText({
    switch(input$operator,
           "+" = input$num1 + input$num2,
           "-" = input$num1 - input$num2,
           "x" = input$num1 * input$num2,
           "/" = input$num1 / input$num2)})
  
}

# Lancer l'application 
shinyApp(ui = ui, server = server)

```

Pour allez plus loin sur ce package Shiny, vous pouvez aller jeter un coup d'oeil sur ces différents liens :

* Le tutoriel [ici](https://shiny.rstudio.com/tutorial/written-tutorial/lesson1/)
* Le tutoriel [ici](https://deanattali.com/blog/building-shiny-apps-tutorial/)

