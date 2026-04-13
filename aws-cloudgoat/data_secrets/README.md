🕵️‍♂️ Crónica de un compromiso: CloudGoat data_secrets
¡Hola! Aquí documento mi paso a paso en el escenario data_secrets. La idea es ir desde un acceso inicial básico hasta la extracción de un secreto sensible en AWS.

📋 Punto de partida

Los datos que recibimos al iniciar el laboratorio son:

Recursos: 1 Usuario IAM, 1 Instancia EC2, 1 Rol IAM, 1 Función Lambda y 1 Secreto en Secrets Manager.

Inicio: AWS Access Key y Secret Key.

Meta (Goal): Obtener la "flag" final guardada en el Secrets Manager.

🚀 Paso 1: Primeros pasos y "¿Quién soy?"
Lo primero es configurar el perfil para empezar a operar. Usamos el comando:
aws configure --profile data_secrets

Antes de movernos, hay que confirmar que las credenciales funcionan y saber exactamente qué usuario somos:
aws sts get-caller-identity --profile data_secrets

🔍 Paso 2: La pared de IAM (Enumeración)
Como buen auditor, lo primero que intenté fue ver qué permisos tengo. Quise listar mis propias políticas, pero... primer muro:

Bash
# Listar políticas adjuntas e inline
aws iam list-user-policies --user-name cg-start-user-[ID] --profile data_secrets
aws iam list-attached-user-policies --user-name cg-start-user-[ID] --profile data_secrets
Recibí un rotundo AccessDenied. El usuario tiene los permisos de IAM bloqueados, lo que nos obliga a "tanteas las paredes" en otros servicios para ver qué podemos ver.

Enumerando recursos

Empecé a lanzar comandos para ver qué había vivo en la cuenta: S3, Lambda, RDS... pero todo me daba error. Hasta que probé con EC2:

Bash
aws ec2 describe-instances --profile data_secrets --query "Reservations[*].Instances[*].{ID:InstanceId,State:State.Name,PublicIP:PublicIpAddress,Role:IamInstanceProfile.Arn}" --output table
¡Encontré algo! Hay una instancia EC2 encendida y, lo más importante, tiene un IAM Role attached. Este es mi "foothold" o punto de apoyo.

🏹 Paso 3: El asalto a la instancia EC2
Tengo una IP pública, así que vamos a ver qué puertos tiene abiertos. Usé nmap para un escaneo rápido:

El puerto 22 (SSH) está abierto. Pero, ¿cómo entro si no tengo llaves? Se me ocurrió revisar el UserData de la instancia, que a veces guarda scripts con información sensible:

Bash
aws ec2 describe-instance-attribute --instance-id [ID] --attribute userData --profile data_secrets
¡Genial! Logré obtener un usuario y password en texto plano:

Usuario: ec2-user

Password: CloudGoatInstancePassword!

⛓️ Paso 4: Escalando dentro de la instancia
Intenté ver si podía modificar los Security Groups para abrir más puertos, pero el sistema me dio AccessDenied nuevamente. Parece que no puedo jugar con la red.

Así que la estrategia fue simple: entrar por SSH con las credenciales que robé del UserData. Una vez dentro, mi objetivo era el IMDS (Metadata Service) para ver si podía "sacarle" las credenciales al rol de la instancia.

Bash
# Consultar el rol y luego sus credenciales
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/cg-ec2-role-[ID]
¡Funcionó! Ahora tengo llaves temporales nuevas. Salí de la instancia, configuré un nuevo perfil en mi Kali (ec2_pwned) y seguí el ataque desde mi máquina.

🔀 Paso 5: El eslabón perdido (La Lambda)
Ya como ec2_pwned, probé ir directo al Secrets Manager, pero me dio AccessDenied. Todavía me faltaba un eslabón. Recordé que el escenario mencionaba una Lambda, así que la listé:

aws lambda list-functions --profile ec2_pwned

¡Bingo! Encontré una filtración clásica: credenciales de un usuario llamado db_user metidas directamente en las variables de entorno de la Lambda. Esto es un error muy común de desarrollo.

🏁 Paso 6: El golpe final al Secrets Manager
Configuré mi tercer perfil: db_user_pwned. Esta era mi última carta para llegar al secreto. Probé listar los secretos y... esta vez sí hubo respuesta:

aws secretsmanager list-secrets --profile db_user_pwned

Ya con el nombre del secreto, solo faltaba leerlo:
aws secretsmanager get-secret-value --secret-id [NOMBRE] --profile db_user_pwned

¡Misión cumplida! Logré llegar a la flag final después de saltar por tres identidades diferentes.

🧠 Reflexiones finales

Este laboratorio me enseñó que la seguridad en la nube es una cadena. Por más que bloquees IAM, si dejas una contraseña en un userData o una Access Key en una variable de entorno de una Lambda, el atacante eventualmente encontrará el camino.

¿Qué aprendí?

No usar contraseñas en UserData.

Usar IMDSv2 para evitar el robo de roles.

No guardar secretos en variables de entorno de Lambda.

Writeup by MartinMarin1