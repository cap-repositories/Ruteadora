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

Por ultimo genere el gerber: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/gengerber_USBA_USBB.png "gengerber"), saldra otro cuadro, en este presione nuevamente generar gerber y se descargara un archivo .zip, que contiene los archivos gerber de la pcb, EasyEDA por defecto guarda la información del borde de la placa en *Gerber_BoardOutline.GKO*, la posición donde se deben hacer los agujeros en *Gerber_Drill_PTH.DRL* y las pistas las guarda por defecto en *Gerber_TopLayer.GTL*.

También se pueden crear las pistas utilizando la herramienta *Auto Router* en linea o desde el computador siguiendo este [enlace](https://docs.easyeda.com/en/PCB/Route/index.html#Local-Auto-Router), para usar esta herramienta se debe considerar el ancho de la pista (**Track width**), el espaciamiento entre las pistas (**Clearance**), el ancho de los pads donde se soldan los pines (**Via diameter**) y el ancho de los orificios que se realizan con el taladro (**Via drill diameter**), este ultimo parametro debe ser menor al **Via diameter** de forma que quede suficiente material para aplicar soldadura.

### <a name="sec_gcode"></a> Generar el codigo G a partir del archivo en formato gerber
Con el archivo en formato gerber, se puede proceder a la generación del código G que dicta como se debe mover la ruteadora de forma que pueda fabricar la pcb. Existen varios programas para realizar esta tarea, ya sea en linea o a través de aplicaciones de escritorio, en esta guía se utiliza [*FlatCAM*](http://flatcam.org/) por sus características y herramientas. Para comenzar a editar los archivos gerber generados en la anterior [sección](#sec_esq), abra FlatCAM y vaya a *file->Open->Open Gerber ...*, abra los archivos *Gerber_BoardOutline.GKO* y *Gerber_TopLayer.GTL*, ahora vaya a *file->Open->Open Excellon ...* y abra el archivo *Gerber_Drill_PTH.DRL*, en FlatCam debera ver lo siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_gerber_USBA_USBB.png "flatcam_gerber")

Lo primero que se necesita hacer es eliminar los elementos innecesarios, para esto busque la pestaña project y de click derecho en *Gerber_Drill_PTH.DRL*, seleccione *edit*, los circulos rojos de la anterior imagen se convertiran en unas cruces, seleccione las cuatro exteriores al circuito y presione suprimir, presione *ctrl+s* para guardar los cambios, aparecera un nuevo archivo con sufijo *_edit*, elimine el que no tiene este sufijo y repita este procedimiento para el archivo *Gerber_TopLayer.GTL*, debera obtener lo siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_gerber1_USBA_USBB.png "flatcam_gerber1")

Como uno de los conectores que se va a usar es de montaje superficial y el otro es de agujero pasante, entonces en la disposición en que se encuentran el gerber con las pistas y el gerber con las posiciones de los agujeros hacen que estén reflejados en X, para poder corregir esto vaya a *Tool->Command Line*, copie y pegue la linea `mirror Gerber_Drill_PTH.DRL_edit -box Gerber_BoardOutline.GKO -axis x` y la linea `mirror Gerber_TopLayer.GTL_edit  -box Gerber_BoardOutline.GKO -axis x`, recuerde presionar enter después de cada linea, el resultado debe ser el siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_gerber2_USBA_USBB.png "flatcam_gerber2")

El siguiente paso es generar un objeto geometría con el cual FlatCAM genera el codigo G. De click izquierdo en el archivo *Gerber_TopLayer.GTL_edit* y vaya a la pestaña *Selected*, busque el parámetro *Tool dia*(diametro de la herramienta) y coloque 0.2mm, presione *FULL Geo*, con esto FlatCAM genera un objeto geometría en el cual se puede visualizar cual va a ser el camino que va a tomar la ruteadora, el resultado debe ser el siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_geo1_USBA_USBB.png "flatcam_geo1")

Ahora para generar el objeto geometría del corte en los limites de la placa, de click izquierdo en el archivo *Gerber_BoardOutline.GKO*, vaya a la pestaña *Selected* y presione *Cutout Tool*, se abrira una nueva pestaña y en esta coloque en los parámetros: 

+ *Tool dia*: 4 (mm).
+ *Gap size*: 0,4 (mm), es el tamaño del espaciamiento entre cortes.
+ *Gaps*: 4, es el tipo de espaciamiento. 

Presione *FULL Geo*. El resultado debe ser el siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_geo2_USBA_USBB.png "flatcam_geo2")

El siguiente elemento a generar es el código G para los agujeros pasantes. De click izquierdo en el archivo *Gerber_Drill_PTH.DRL_edit*, vaya a la pestaña *Selected*, coloque en los parametros:

+ *Cut Z*: -2.2 (mm), es el desplazamiento en Z para realizar el corte.
+ *Travel Z*: 5 (mm), es el desplazamiento en Z para posicionar la herramienta en otro punto sin realizar corte.
+ *End move Z*: 50 (mm), es la posición en Z de la herramienta cuando termina.
+ *Feedrate Z*: 3 (mm/min), es la velocidad del movimiento en Z cuando realiza la perforación.
+ *Spindle speed*: 12000 (RPM), es la velocidad del huso.

y presione *Create Drills GCode*. Ahora, genere el código G para las pistas. De click izquierdo en el archivo *Gerber_TopLayer.GTL_edit_iso*, vaya a la pestaña *Selected*, coloque en los parámetros:

+ *Cut Z*: -0.3 (mm), es la profundidad necesaria para que la herramienta pueda quitar la capa de cobre.
+ *Travel Z*: 5 (mm).
+ *End move Z*: 50 (mm).
+ *Feedrate X-Y*: 10 (mm/min), es la velocidad del movimiento sobre el plano X-Y.
+ *Feedrate Z*: 3 (mm/min).
+ *Spindle speed*: 12000 (RPM).

y presione *Generate*. Ahora, genere el código G para las pistas. De click izquierdo en el archivo *Gerber_BoardOutline.GKO_cutout*, vaya a la pestaña *Selected*, coloque en los parámetros:

+ *Cut Z*: -2 (mm), es la profundidad necesaria para que la herramienta pueda cortar la pcb.
+ *Travel Z*: 5 (mm).
+ *End move Z*: 50 (mm).
+ *Feedrate X-Y*: 3 (mm/min).
+ *Feedrate Z*: 3 (mm/min).
+ *Spindle speed*: 12000 (RPM).

y presione *Generate*. Con lo realizado hasta ahora el resultado es el siguiente:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/flatcam_gcode_USBA_USBB.png "flatcam_gcode")

note que en la pestaña *project* del panel izquierdo, en la categoria *CNC Job* deben haber 3 archivos: *Gerber_TopLayer.GTL_edit_iso_cnc*, *Gerber_Drill_PTH.DRL_edit_cnc* y *Gerber_BoardOutline.GKO_cutout_cnc*, que son respectivamente el codigo G de las pistas, los agujeros pasantes y el corte de los bordes. De click izquierdo en cada uno, seleccione *save* y guarde el archivo como pistas, agujeros y corte respectivamente.

### <a name="sec_prepare"></a> Preparar la ruteadora
Para realizar el grabado de las pistas, los agujeros pasantes y el corte de la pcb, como se ha configurado en los pasos anteriores, va a necesitar una "fresa v 30 grados para collect de 3mm", una "broca 1mm para collet de 3mm" y una "fresa de 4mm para collet de 6mm", estos elementos se muestran en la siguiente imagen:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/broca_fresas_USBA_USBB.png "brocas y fresa")

Y para colocar estas herramientas va a necesitar un collet para fresas de 3mm y otro para fresas de 6mm:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/collects_USBA_USBB.png "collets")

La ruteadora tiene dos interruptores para controlar la energía que llega a los motores de movimiento, asegurese de que estos esten encendidos y además asegurese de desconectar el motor donde van las fresas, como se muestra en la imagen:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/encenderRuteadora_USBA_USBB.png "encender motores de la ruteadora")

Conecte el cable USB (del arduino) en una computadora y abra **OpenCNCPilot**, una vez se abra la interfaz, a la derecha encontrara un recuadro que tiene como titulo "Machine", desplieguelo y presione *Connect*: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/connect_opencncpilot_USBA_USBB.PNG "connect opencncpilot"), revise si en recuadro del centro aparece un boton con el texto *Alarm*: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/alarm_opencncpilot_USBA_USBB.PNG "alarm opencncpilot"), este mensaje aparece cuando se resetea el arduino o cuando se encuentra un fallo y para poder manipular la ruteadora se debe cambiar este estado, para cambiar el estado presione el boton y debera cambiar de *Alarm* a *Idle*: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/idle_opencncpilot_USBA_USBB.PNG "idle opencncpilot"), ahora se debe llevar la ruteadora a los puntos de referencia, para hacer esto asegurese que no se encuentren elementos que puedan obstaculizar el movimiento de la ruteadora, luego presione *Home Machine*: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/home_opencncpilot_USBA_USBB.PNG "home opencncpilot"), la ruteadora comenzara a buscar los puntos de referencia, note que en el recuadro "Machine" comenzara a llenarse el *buffer*: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/buffer_opencncpilot_USBA_USBB.PNG "buffer opencncpilot"), este indica si hay tareas pendientes y cuantas tareas más puede recibir, en caso de que la maquina no responda a los comandos o que pueda llegar a un estado peligroso presione *Soft Reset* en el recuadro del centro: ![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/softreset_opencncpilot_USBA_USBB.PNG "soft reset opencncpilot"), esto detendra la maquina y limpiara el *buffer*.

Una vez la ruteadora haya encontrado los puntos de referencia esta debera detenerse y el *buffer* debera estar vacio. Utilice los sujetadores que se encuentran en la ruteadora:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/sujetadorpcb_USBA_USBB.png "sujetador pcb")

Y sujete la pcb a la ruteadora, asegurese de colocar una pedazo de madera debajo de la pcb y que esta quede bastante firme para evitar que en la etapa de corte se mueva. La pcb puede quedar de la siguiente forma:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/sujetarpcb_USBA_USBB.png "pcb sujetada")

Ahora instale la "fresa v" (recuerde que la fresa se debe instalar con el collet de 3mm) y conecte los caimanes como se muestra en la imagen:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/colocar_probadores_USBA_USBB.png "pcb probadores")

Estos son utilizados para determinar el mapa de altura de la pcb. El siguiente paso es posicionar los motores de la ruteadora, en el recuadro con titulo "Manual", desplieguelo y presione en *Jogging Keyboard Focus*, mantenga el mouse sobre este boton para ver las instrucciones de uso, utilizando la función de *Jogging* posicione la ruteadora de forma que tenga suficiente espacio para hacer la tarea de corte, cuando la fresa toque la pcb en la interfaz grafica de **OpenCNCPilot** aparecera el texto "Probe" en rojo, cuando considere que la ruteadora ya esta en la posición correcta vaya al recuadro "Manual" y presione *Zero (G10)* luego presione *Send* para hacer que la posición actual de la ruteadora sea el cero relativo a las tareas que se realizaran posteriormente, en la siguiente imagen se muestra como podría quedar posicionada la ruteadora:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/heightmap_USBA_USBB.png "pcb posinicial")

Para verificar que el espacio es suficiente vaya al recuadro *File*, desplieguelo y presione *Open* para cargar el codigo *corte.nc*, la interfaz de **OpenCNCPilot** debera estar como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/punto_inicial_USBA_USB_B.png "opencncpilot posinicial")

utilizando la función de *Jogging*, levante del spindle de forma que el texto "Probe" desaparezca y mueva la ruteadora para verificar que el cero relativo es adecuado, si no cambielo y vuelva a verificar, una vez encuentre el cero adecuado anote los parámetros *MX*, *MY* y *MZ* en el recuadro central, con estos podrá volver a posicionar la ruteadora en el cero relativo que selecciono en caso de que tenga desconectar o reiniciar la ruteadora (recuerde primero encontrar los puntos de referencia utilizando el boton *Home Machine*). Ahora vaya al recuadro "Probing", desplieguelo y presione *Create new*, se abrira una ventana, en esta presione *Size From GCode* y presione *Ok*, deberan aparecer unos puntos rojos y unas lineas verdes como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/heightmap_opencncpilot_USBA_USBB.PNG "opencncpilot heightmap")

Estas son utilizadas por el software para mover la herramienta y crear el mapa de altura con el cual se realizan las correcciones al codigo G necesarias para grabar a la profundidad que se programó. En el recuadro "Probing" presione el boton "Run" y la ruteadora comenzará a seguir los puntos rojos y encontrara la diferencia de altura con respecto al cero de referencia que tiene cada punto, una vez haya acabado este proceso la interfaz de **OpenCNCPilot** debera estar como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/heightmap_end_opencncpilot_USBA_USBB.PNG "opencncpilot heightmap end")

Vaya al recuadro "Probing" y presione *Save*, se abrira una ventana con la cual podrá crear un archivo con la información del mapa de alturas de forma que si se pierde esta información la pueda cargar utilizando el boton *Load* en el recuadro "Probing". Por ultimo retire los caimanes de prueba, conecte el spindle (que desconectó previamente) y la ruteadora estará lista para realizar el grabado de las pistas.

### <a name="sec_execute"></a> Ejecutar el codigo G generado
Se debe comenzar realizando el grabado de las pistas, para esto vaya al recuadro "File" y presione *Open*, en la ventana busque el archivo *pistas.nc* y abralo, la interfaz de **OpenCNCPilot** debera estar como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/tracks_opencncpilot_USBA_USBB.PNG "opencncpilot heightmap end")

Ahora vaya al recuadro "Edit", desplieguelo y presione *Apply HeightMap*, luego vaya al recuadro "File" y presione *Start*, la ruteadora debera comenzar a realizar el grabado de las pistas. Si en algún momento nota que algo no anda bien y quiere parar el proceso, presione *Pause* y luego *Hold* o *Soft Reset*, en caso de que quiera limpiar el *buffer*, luego vuelva a cargar el archivo con las pistas, aplique el mapa de alturas e inicie el proceso.

Una vez la ruteadora haya terminado las pistas, utilice la función de *Jogging* para colocar el spindle en una posición en la que pueda cambiar la fresa (solo mueva la ruteadora en Z) y coloque la broca de 1mm (recuerde que la broca se debe instalar con el collet de 3mm), como esta herramienta puede ser un poco más grande que la fresa debera ajustar el cero de la ruteadora, para esto posicione el spindle de forma que toque ligeramente la pcb, como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/zero_agujerospcb_USBA_USBB.png "zero broca")

Vaya al recuadro "Manual" (minimize los recuadros necesarios) y presione *Zero (G10)*, luego abra el archivo *agujeros.nc* de la misma forma en la que abrio el archivo *pistas.nc* anteriormente y presione *Start*.

Cuando la ruteadora haya acabado de hacer los agujeros pasantes, nuevamente coloque el spindle en una posición que pueda cambiar la broca por la fresa de 4mm (recuerde que la fresa se debe instalar con el collet de 6mm) y posicione el spindle de forma que toque ligeramente la pcb, como se muestra a continuación:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/zero_cortepcb_USBA_USBB.png "zero fresa")

y nuevamente ajuste el cero presionando *Zero (G10)*, luego abra el archivo *corte.nc* de la misma forma en la que abrio el archivo *pistas.nc* anteriormente y presione *Start*. Cuando la ruteadora acabe podra retirar el recorte de la pcb, al lijarlo y quitar el exceso de material debera quedar de la siguiente forma:

![alt text](https://github.com/cap-repositories/Ruteadora/blob/master/miscelanea/imagenes/pcbFinal_USBA_USBB.png "pcb final")
