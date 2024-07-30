# Plantilla de AWS CloudFormation

Esta plantilla de AWS CloudFormation crea una instancia EC2 junto con los grupos de seguridad necesarios y el mapeo de dispositivos de bloque. La instancia EC2 se puede configurar con diferentes tipos de instancia, tamaños de volumen y tipos de volumen según las necesidades.

## Parámetros

- `mykeypair`: Nombre del par de claves para acceder a la instancia.
- `InstanceTypeParameter`: Tipo de instancia EC2. Valores permitidos: `t2.micro`, `m1.small`, `m1.large`. El valor por defecto es `t2.micro`.
- `SecurityGroupIP`: IP para hacer whitelist en nuestro grupo de seguridad. El valor por defecto es `0.0.0.0/0`.
- `VolumeSize`: Tamaño del volumen EBS en GB. El valor por defecto es `20`.
- `VolumeType`: Tipo de volumen EBS. Valores permitidos: `gp2`, `gp3`, `io1`. El valor por defecto es `gp3`.
- `SSHFromIP`: La IP desde la que se permiten conexiones SSH. El valor por defecto es `0.0.0.0/0`.
- `HTTPFromIP`: La IP desde la que se permiten conexiones HTTP. El valor por defecto es `0.0.0.0/0`.
- `HTTPSFromIP`: La IP desde la que se permiten conexiones HTTPS. El valor por defecto es `0.0.0.0/0`.

## Recursos

### Instancia EC2

- **MyInstance**: Esta es la instancia EC2 que se creará.
  - `ImageId`: `ami-0648742c7600c103f` (ID de la imagen AMI para la instancia).
  - `InstanceType`: Se define mediante el parámetro `InstanceTypeParameter`.
  - `KeyName`: Se define mediante el parámetro `mykeypair`.
  - `BlockDeviceMappings`: Configuración del volumen EBS.
    - `DeviceName`: `/dev/sdm`
    - `Ebs`:
      - `VolumeType`: Se define mediante el parámetro `VolumeType`.
      - `DeleteOnTermination`: `"true"`
      - `VolumeSize`: Se define mediante el parámetro `VolumeSize`.
  - `SecurityGroupIds`:
    - `ServerSecurityWeb`
    - `ServerSecurityPrivate`

### Grupos de Seguridad

- **ServerSecurityWeb**: Grupo de seguridad que permite conexiones desde sitios públicos especificados.
  - `GroupDescription`: "Allow connections from specified public sites"
  - `SecurityGroupIngress`:
    - Puerto `80` (HTTP) desde `HTTPFromIP`.
    - Puerto `443` (HTTPS) desde `HTTPSFromIP`.

- **ServerSecurityPrivate**: Grupo de seguridad que permite conexiones desde sitios locales especificados.
  - `GroupDescription`: "Allow connections from specified local sites"
  - `SecurityGroupIngress`:
    - Puerto `22` (SSH) desde `SSHFromIP`.

## Uso

Para usar esta plantilla, sigue los siguientes pasos:

1. Guarda el código de la plantilla en un archivo con extensión `.yaml` o `.json`.

2. Sube la plantilla a AWS CloudFormation:
   - Ve a la consola de AWS CloudFormation.
   - Haz clic en "Create stack".
   - Selecciona "Template is ready" y "Upload a template file".
   - Sube el archivo de la plantilla.

3. Configura los parámetros según tus necesidades.

4. Revisa y crea la pila (stack).

## Ejemplo de comando AWS CLI

Puedes usar el siguiente comando de AWS CLI para crear una pila usando esta plantilla:

```bash
aws cloudformation create-stack --stack-name MiNuevaInstancia --template-body file://ruta/a/tu/plantilla.yaml --parameters ParameterKey=mykeypair,ParameterValue=tu-par-de-claves ParameterKey=InstanceTypeParameter,ParameterValue=t2.micro ParameterKey=VolumeSize,ParameterValue=20 ParameterKey=VolumeType,ParameterValue=gp3 ParameterKey=SSHFromIP,ParameterValue=0.0.0.0/0 ParameterKey=HTTPFromIP,ParameterValue=0.0.0.0/0 ParameterKey=HTTPSFromIP,ParameterValue=0.0.0.0/0
