# AWS-CLI EC2 Apache Web Server
[Quelle](https://towardsaws.com/how-to-use-aws-cli-to-launch-an-ec2-web-server-with-apache-9c20d07e07be)

### Cloud9 mit GitHub SSH Verbinden/verknüpfen

### Projekt erstellen 

#### Projektverzeichniss erstellen

    mkdir AWSCLI
    
#### Textdatei fuer Ressourcen erstellen

    touch resources.txt

#### Ueberpruefen AMI

    aws ec2 describe-images --owners amazon --filters "Name=name, Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2" "Name=state, Values=available" --query "reverse(sort_by(Images, &Name))[:1].ImageId" --output text
    
#### Erstellen Key par fuers Projekt

    aws ec2 create-key-pair --key-name TB_Apache --query 'KeyMaterial' --output text > TB_Apache.pem

#### Ueberpruefen 

    ls -lah
    
#### Beschreibung von KeyPar

    aws ec2 describe-key-pairs --key-name TB_Apache --output json
    
#### Security Group Estellen

    aws ec2 create-security-group --group-name TB_Apache_SG --description "Allows SSH, HTTP and HTTPS connections for the Web Server"
    
#### security Group ID:

    sg-05560bcfee7182025

#### Add Rules to the Security Group
Fügen Sie nun eine Regel hinzu, um Netzwerkdatenverkehr auf TCP-Port 22 fuer SSH-Verbinden

##### Port 22

    aws ec2 authorize-security-group-ingress --group-id  sg-05560bcfee7182025  --protocol tcp --port 22 --cidr 0.0.0.0/0
    
##### Port 80

    aws ec2 authorize-security-group-ingress --group-id  sg-05560bcfee7182025  --protocol tcp --port 80 --cidr 0.0.0.0/0

##### Port 443

    aws ec2 authorize-security-group-ingress --group-id  sg-05560bcfee7182025  --protocol tcp --port 443 --cidr 0.0.0.0/0
    
#### Ueberpruefen Sie nun die für Sicherhetsgruppe erstellten Regeln 

    aws ec2 describe-security-groups --group-ids sg-05560bcfee7182025 --output Table
    
#### Erstellen Bash-Skript-Datei (webscript.sh)

    touch webscript.sh

#### Esrtellen Sie Zulest eine EC2-Instance, indem Sie Befehl ausführen

    aws ec2 run-instances --image-id ami-0b920b0594b5288fb --count 1 --instance-type t2.micro --key-name TB_Apache --security-group-ids sg-05560bcfee7182025 --user-data file://webscript.sh
 
#### Überprüfen Sie Ihre installierte EC2-Instanz und den Apache-Webserver

    aws ec2 describe-instances
    
#### File Permission    

    chmod 400 TB_Apache.pem    
    
#### EC2-instance Mit ssh verbinden 

    ssh -i TB_Apache.pem ec2-3-68-78-69.eu-central-1.compute.amazonaws.com