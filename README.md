# Ruteadora
Este repositorio esta pensado para que las personas que quieran conocer o tengan la posibilidad de usar la ruteadora del CAP, se documenten acerca de su funcionamiento y puedan aprender a usarla de forma independiente.

## Componentes
*hablar acerca de las partes de la ruteadora, mostrar imagenes y mencionar el software que se encuentra instalado*

## Software de control
Esta ruteadora es pensada para trabajar principalmente en la fabricación de PCBs, por lo cual se recomienda la instalación de [OpenCNCPilot](https://github.com/martin2250/OpenCNCPilot) que envia el codigo G al controlador de la ruteadora a través de un puerto serial.

## Guía para realizar una PCB prototipo
Para realizar un circuito impreso utilizando la ruteadora se deben realizar los siguientes pasos:

0. [Consideraciones generales](#sec_notes)
1. [Crear el esquematico y generar el archivo en formato gerber](#sec_esq)
2. [Generar el codigo G a partir del archivo en formato gerber](#sec_gcode)
3. [Preparar la ruteadora](#sec_prepare)
4. [Ejecutar el codigo G generado](#sec_execute)


Como ejemplo, se mostrará como se realizó una PCB prototipo en la cual se instalan dos conectores USB hembra, uno tipo A y el otro tipo B, para crear una interfaz entre un cable con terminal USB A macho y otro con terminal USB B macho.

### <a name="sec_notes"></a> Consideraciones generales

### <a name="sec_esq"></a> Crear el esquematico y generar el archivo en formato gerber
Para crear el esquematico se puede utilizar cualquier software de automatización de diseño electrónico (**EDA**) como *Altium*, *Eagle*, *KiCad*, *EasyEDA*, entre otros... lo importante es poder diseñar un circuito y generar un archivo en formato [gerber](https://es.wikipedia.org/wiki/Gerber_(formato_de_archivo)) que contiene la información necesaria para fabricar la PCB. En está guía se utiliza *EasyEDA* porque se encuentra disponible en linea y es suficiente para la aplicación, para esta aplicación solo se necesitan dos componentes: *USB A* y *USB B*, que se pueden encontrar en la sección *Libraries* de EasyEDA. Luego de colocar los componentes y unirlos con cables el esquematico queda de la siguiente forma:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/schematic_USBA_USBB.PNG "esquematico")

Después debes convertir a pcb el esquematico: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/convertPCB_USBA_USBB.png "convertir a pcb"), saldra una nueva ventana que muestra la pcb lista para posicionar y crear las pistas:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/PCB1_USBA_USBB.PNG "pcb1")

el recuadro morado que aparece en la anterior imagen representa los bordes de la pcb, para ajustar los bordes vaya a configuración: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/configOutline_USBA_USBB.png "configurar bordes") y coloque de ancho 20mm, de alto 14mm, el inicio en X en 0mm y el inicio en Y 14mm, ahora posicione los componentes aproximadamente de la siguiente manera:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/PCB2_USBA_USBB.PNG "pcb2")

Seleccione cada pad de la USB A, en un recuadro a la derecha aparecen las propiedades del pad, ajuste la altura del pad a 1.2mm, ahora seleccione la herramienta de pistas del pcb tools: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/tracktool_USBA_USBB.png "tracktool"), y en el cuadro de propiedades ajuste el ancho de la pista a 1mm, es recomendable utilizar este ancho de pista por las limitaciones de la ruteadora y también es bueno que los pads de los elementos tengan un ancho mayor al ancho de la pista, al trazar las pistas el circuito debe quedar de la siguiente manera:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/PCB3_USBA_USBB.PNG "pcb3")

Por ultimo genere el gerber: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/gengerber_USBA_USBB.png "gengerber"), saldra otro cuadro, en este presione nuevamente generar gerber y se descargara un archivo .zip, que contiene los archivos gerber de la pcb, EasyEDA por defecto guarda la información del borde de la placa en *Gerber_BoardOutline.GKO*, la posición donde se deben hacer los orificios en *Gerber_Drill_PTH.DRL* y las pistas las guarda por defecto en *Gerber_TopLayer.GTL*.

También se pueden crear las pistas utilizando la herramienta *Auto Router* en linea o desde el computador siguiendo este [enlace](https://docs.easyeda.com/en/PCB/Route/index.html#Local-Auto-Router), para usar esta herramienta se debe considerar el ancho de la pista (**Track width**), el espaciamiento entre las pistas (**Clearance**), el ancho de los pads donde se soldan los pines (**Via diameter**) y el ancho de los orificios que se realizan con el taladro (**Via drill diameter**), este ultimo parametro debe ser menor al **Via diameter** de forma que quede suficiente material para aplicar soldadura.

### <a name="sec_gcode"></a> Generar el codigo G a partir del archivo en formato gerber

### <a name="sec_prepare"></a> Preparar la ruteadora

### <a name="sec_execute"></a> Ejecutar el codigo G generado
