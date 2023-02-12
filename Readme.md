# Dashboard para Encuesta de salarios basado en los datos de AskAManager.org

Ofrece un panorama general de salarios a nivel mundial por industria y tipo de cargo a través de un dashboard construido con Looker Studio™ basado en los datos recopilados por AskAManager.org.

La interface de captura de la encuesta es a través de un formulario de Google Forms con los siguientes campos:

 - How old are you?
 - What industry do you work in?
 - Job title
 - If your job title needs additional context, please clarify here:
 - What is your annual salary? (You'll indicate the currency in a later question. If you are part-time or hourly, please enter an annualized equivalent -- what you would earn if you worked the job 40 hours a week, 52 weeks a year.)
 - How much additional monetary compensation do you get, if any (for example, bonuses or overtime in an average year)? Please only include monetary compensation here, not the value of benefits.
 - Please indicate the currency
 - If "Other," please indicate the currency here: 
 - If your income needs additional context, please provide it here:
 - What country do you work in?
 - If you're in the U.S., what state do you work in?
 - What city do you work in?
 - How many years of professional work experience do you have overall?
 - How many years of professional work experience do you have in your field?
 - What is your highest level of education completed?
 - What is your gender?
 - What is your race? (Choose all that apply.)

Adicional a esta información, por cada registro se almacena la fecha y hora en la que se realizó la encuesta y por tanto, se encuentra disponible en el dashboard para filtrar la información.

### Modelo de datos

A continuación se describen los atributos del modelo de datos tras el análisis a la información capturada a través de la encuesta.

|Variable original|Atributo|Función de agregación predeterminada|Tipo de dato|Descripción|Nuevo?|
|---|---|---|---|---|---|
|How old are you?|age|Texto|Ninguna|La edad del participante en la encuesta||
|What is your annual salary? (You'll indicate the currency in a later question. If you are part-time or hourly, please enter an annualized equivalent -- what you would earn if you worked the job 40 hours a week, 52 weeks a year.)|annual_salary|Flotante|Total|El salario anual estimado. Si trabaja a tiempo parcial o por horas, indica un equivalente anual, es decir, lo que ganaría si trabajara en el puesto 40 horas a la semana, 52 semanas al año||
||annual_salary_COP|Moneda|Total|El valor del salario anual expresado en Pesos colombianos|🆕|
|What city do you work in?|city|Texto|Ninguna|Ciudad en la que trabaja (Valor original)||
||city_standardized|Texto|Ninguna|Ciudad en la que trabaja (Nombre estándar según ISO 3166)|🆕|
|What country do you work in?|country|Texto|Ninguna|País en el que trabaja (Valor original)||
||country_standardized|Texto|Ninguna|País en el que trabaja (Nombre estándar según ISO 3166)|🆕|
||country_suggested|Texto|Ninguna|País en el que trabaja (Nombre sugerido en una primera etapa de limpieza de los datos)|🆕|
|Please indicate the currency|currency|Texto|Ninguna|Moneda en el que el usuario expresó el salario (Abreviatura estándar según ISO 4217)||
|If "Other," please indicate the currency here: |currency_other|Texto|Ninguna|Moneda en la que está expresado el salario diferente a las opciones mostrada en la encuesta||
||currency_precleaned|Texto|Ninguna|Moneda en el que el usuario expresó el salario (Abreviatura estándar según ISO 4217 asignada durante el proceso de limpieza)|🆕|
|Timestamp|date|Fecha-Hora|Ninguna|Fecha y hora de la encuesta||
|What is your highest level of education completed?|education_level|Texto|Ninguna|Nivel de escolaridad más alto alcanzado||
|How much additional monetary compensation do you get, if any (for example, bonuses or overtime in an average year)? Please only include monetary compensation here, not the value of benefits.|estimated_benefits_amount|Moneda|Total|Valor estimado de beneficios adicionales al salario||
||estimated_benefits_amount_COP|Moneda|Total|Valor estimado de beneficios adicionales al salario expresado en Pesos colombianos|🆕|
|How many years of professional work experience do you have in your field?|experience_in_field|Texto|Ninguna|Años de experiencia en el campo profesional particular||
|How many years of professional work experience do you have overall?|experience_overall|Texto|Ninguna|Nivel de escolaridad más alto alcanzado||
|What is your gender?|gender|Texto|Ninguna|Género del participante en la encuesta||
||id|Entero|Total|Identificador del registro de encuesta||
|If your income needs additional context, please provide it here:|income_context|Texto|Ninguna|Observaciones sobre los ingresos||
|What industry do you work in?|industry|Texto|Ninguna|Industria en la que desempeña el cargo||
|If your job title needs additional context, please clarify here:|job_context|Texto|Ninguna|Observaciones sobre el cargo que desempeña||
|Job title|job_title|Texto|Ninguna|Cargo que desempeña||
||job_title_standardized|Texto|Ninguna|Cargo que desempeña (Estandarizado durante el proceso de limpieza)|🆕|
|What is your race? (Choose all that apply.)|race|Texto|Ninguna|Raza||
|If you're in the U.S., what state do you work in?|state|Texto|Ninguna|Estado.  (Aplica para Estados Unidos)||
||total_salary_income|Moneda|Total|Suma de annual_salary_COP y estimated_benefits_amount_COP|🆕|

### ¿Cómo actualizar los datos?
El proceso de modelado está soportado en el Jupyter notebook DataCleaning.ipynb que puede encontrar en este mismo repositorio al que se le especifica una ruta o URL de la que se debe leer el archivo en formato csv con las encuestas actualizadas, al final generará un archivo en formato Excel para ser cargado o actualizado en Looker Studio para actualiar la información del dashboard.

A continuación se describen los pasos que se llevan a cabo en el notebook mencionado:

 1. Lectura del archivo
    > Usando la librería Pandas, se crea un DataFrame a partir del conjunto de datos original descargado de AskAManager.org.
 2. Revisión de tipos de datos y estadísticas descriptivas
    > Usando la librería Pandas, se revisan los tipos de datos de cada columna y las estadísticas descriptivas como mínimos, máximos, desviación estándar y percentiles
 3. Asignación de diccionarios de Países, Ciudades y Monedas y estandarización de los valores en las respectivas columnas
    > Se toma de la fuente de datos externa https://datahub.io la lista estándar de Países, Ciudades y Monedas, la cual se carga a un dataframe cada una.  Posteriormente se procede a aplicar una limpieza de los países basado en una verificación previa de los valores incorrectos que podrían darse para cada país. Luego, usando la librería FuzzyWuzzy, se trata de ajustar los países mal escritos tomando como referencia la lista estándar de países. Los países que no puedan asociarse al nombre estándar se asignará el valor __Other__.
    > La misma función se aplica sobre las ciudades y las monedas.
 4. Consultar las tasas de cambio tomando como referencia la moneda Pesos colombianos
    > Usando la fuente de datos externa https://v6.exchangerate-api.com, se consultan a través del API que proveen los valores más recientes de las tasa de cambio para una moneda de referencia.  Por definición, la moneda dereferencia para el dashboard es COP.  Para cada encuesta, se toma la moneda especificada por el usuario y se crean nuevos atributos en el modelo de datos con los valore sde salario y beneficios expresados en COP. Aquellas encuestas para las que no se encontró una tasa de cambio, son excluidas del conjunto de datos.  Finalmente se crea un atributo que sume el salario y los beneficios en COP.
 5. Se evalúan los valores atípicos de salarios y beneficios adicionales
    > Se obtienen de nuevo estadísticas descriptivas del conjunto de datos y se excluyen los valores de salario y beneficios sospechosamente muy bajos o muy altos, ya que pueden tratarse de valores atípicos por error de captura o incosnsitencia entre el valor y el tipo de moneda seleccionada.
 6. Estandarización de los cargos
    > Usando la librería spiCy y el diccionario en_core_web_sm, se hace uso de técnicas de NLP para poder extraer de las descripciones de los cargos, las profesiones o palabras base que identifican el cargo.  Esto con el fin de disminuir la variedad de cargos, por ejemplo, Marketing Manager, Financial Manager, IT Manager, se clasifican como Manager únicamente. Esto también facilita la interacción del usuario en el dashboard.
 7. Generación del archivo para cargar en Looker Studio
    > Finalmente el conjuto de datos depurado se exporta en formato Excel quedando listo para ser cargado en Looker Studio.
