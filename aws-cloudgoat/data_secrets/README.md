Comenzamos el laboratorio data_secrets de Cloudgoat. Los unicos datos de inicio que tenemos son:

Scenario Resources:

1 IAM User
1 EC2 Instance
1 IAM Role
1 Lambda Function
1 Secrets Manager Secret

Scenario Start(s):

AWS Access Key and Secret Key

Scenario Goal(s):

Retrieve the final flag stored in the AWS Secrets Manager.

Luego de desplegar el laboratorio obtenemos los datos de inicio, y asumimos el usuario con el comando: 

aws configure --profile data_secrets

![data_secrets](<assests/Secrets Keys - data_secrets.png>)

FASE DE ENUMERACION

Antes de intentar asumir un rol, debes saber qué permisos tiene el usuario inicial. Un comando vital para cualquier auditoría es saber "quién soy":

aws sts get-caller-identity --profile data_secret

![sts get-caller-identity](<assests/sts get-caller-identity.png>)

Vemos que el usuario existe. Como primer paso que tenemos que tener en cuenta en estos casos, es saber con que permisos contamos para poder asumir. Tratemos de ver que politicas de IAM puede ver el usuario:

# Listar políticas adjuntas al usuario
aws iam list-user-policies --user-name cg-start-user-cgidj0d4rxfr7x --profile data_secret

# Listar políticas gestionadas (inline)
aws iam list-attached-user-policies --user-name cg-start-user-cgidj0d4rxfr7x --profile data_secret

No tenemos permisos suficientes, la salida de los comandos nos da (AccessDenied). El usuario tiene probablmente los permisos de IAM Bloqueados, lo que dificulta la enumeracion de permisos en la cuenta.

La etapa de enumeracion continua. El proximo paso es enumerar recursos. Pasamos a ver que recursos podemos enumerar y si eventualmente podemos obtener algun tipo de nombre de rol o policy.
