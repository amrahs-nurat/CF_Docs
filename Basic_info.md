#### Infrastructure as Code:
- Process of provisioning and managing infrastructure using code.
- Human readable and machine comsumable.
> In AWS Two tools works as IaC:
- CloudFormation
- Cloud Development kit
#### Benefits of Infra as code:
- Visibility 
- Stability
- Scalability
- Security
- Transactional
#### What is CloudFormation.
- Infrastructure as code service for AWS.
- Template to spin up AWS Infrastructure- Multiple Accounts, Dev, Stage, Prod, DR etc.
- written in Json or YAML.
- Free of Charge, `Only Pay for the Provisioned infrastructure`
```
##### Sample Template:
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  S3Bucket: 
    Type: AWS::S3::Bucket 
Outputs: 
  BucketName: 
    Value: !Ref 'S3Bucket'
    Description: Name of the sample Amazon S3 Bucket.
```
#### CloudFormation Lingos:
> Template:
    - Blueprints for AWS Resources, which is written on Json or Yaml.
> Stack:
    - Stacks are created by submiiting Template, stacks can be created, updated and deleted.
> Change Set:
    - Summary of Proposed changes in Stack and How the change will impact on resources.

#### Ways to Run CloudFormation:
- Aws CloudFormation Console
- CloudFormation API
    1. Anything that can call API- Lambda, Ec2, AWSCLI etc.
- DevOps Tools
    1. Jenkins
        - CloudFormaton Plugin 
        - Pipeline
    2. Terraform
    3. AWS CodePipeline
- Other Tools/services -COZ API!
#### Cost of your CloudFormation Stack:
- CloudFormation itself is free.
- You only pay for the resources spun up by CloudFormation (if not eligible by free tier)
> How to calculate cost of stack:
create a sample stack--> Search to cloudFormation -> Create Stack -> select a sample template -> Lamp stack -> provide details -> In Review 'Click on  Estimate Cost' -> you will get info now 
#### JSON vs YAML:
1. JSON: JavaScript Object Notation
- Lightweight Format for storing and transporting data
2. YAML: Yaml Ain't Markup Language
- Human-readable data-serialization language.
#### CloudFormation Template Anatomy: 
- AWSTemplateFormatVersion
- Description
- Metadta
- Parameters
- Mappings
- Conditions
- Transform
- Resources --> `Mandatory Required Field`
- Outputs
#### General information about Parameters:
- Max of 60 Parameters
- Name must be Alphanumeric and Unique
- Each Parameter must have a Type
    1. String
    2. Number (integer or float)
    3. List <Number>
    4. CommaDelimitedList (["test","dev","Prod",])
    5. AWS-Specific Parameter Types
    6. SSM Parameters Types
    7. Parameter Converted to string for referring elsewhere
### Declaration of Parameters: 
```
Parameters:
  DBPort: 
    Type: Number
    Default: "3306"
    Description: "Tcp/IP Port for the database"
    MinValue: '1150'
    MaxValue: '65535'
  DBPwd: 
    Description: "Please enter password for database"
    NoEcho: True 
    Type: string 
    MinLength: '1'
    MaxLength: '40'
    AllowedPattern: "^[a-zA-Z0-9]*$"
-------------
Parameters:
    SNSEmail:
        Type: String
        Description: Enter email for SNS notification
```
#### AWS-Specific Parameter Types:
- Predefined Parameters
- Example- AWS::EC2::KeyPair::KeyName
  1. validates input against existing key pair names
```
Parameters:
  myKeyPair: 
    Description: "Amazon EC2 Key Pair" 
    Type: "AWS::EC2::KeyPair::KeyName"
  mySubnetID: 
    Description: "Subnet id" 
    Type: "List<AWS::EC2::Subnet::ID>"
```
> AWS-Specific Parameter Types:
- AWS::EC2::KeyPair::KeyName
- AWS::EC2::Subnet::ID
- AWS::EC2::VPC::ID
- AWS::EC2::Instance::ID
- AWS::EC2::Image::ID
- AWS::EC2::SecurityGroup::ID
> Intrinsic Function: `Ref` (This is used to define Parameters)
#### Pseudo Parameters:
- Predefined by AWS CloudFormation
- Use Intrinsic Function Ref to retrieve values
```
Ex: 
Outputs:
    stackRegion:
        Value: !Ref "AWS::Region"
```
> Popular Pseudo Parameters:
- AWS::Region
- AWS::AccountId
- AWS::NotificationARNs
- AWS::StackId
- AWS::StackName
#### Mappings
- Key value Mapping
- Use Intrinsic function `Fn::FindInMap to retrieve values 
> Fn::FindInMap
FindInMap:
    MapName:   `Logical Name of Mapping`
        TopLevelKey   `Top-Level Key Name`
        SecondLevelKey  `Second_level Key Name, set to one of the keys from the list assigned to TopLevelKey`
```
--- 
Mappings: 
  RegionMap: 
    us-east-1: "RHEL: ami-012abc"   #RHEL is second Levelkey
    us-west-1: "RHEL: ami-012456789abc"
```
#### Conditions
- Define conditions based on which entities are created or configured.
- E.g. - Create a resource when certain condition is true.
```
Conditions:
    Logical ID:
        Intrinsic function
```
> Some of the Conditions:
- Fn::And
- Fn::Equals
- Fn::If
- Fn::Not
- Fn::Or
```
Parameters:
    EnvType:
        AllowedValues:
            - test
            - prod
        Default: test
        Description: Environment Type
        Type: String

Conditions:
    CreateProdResources:
        'Fn::Equals':
            - Ref EnvType
            - prod

Resources:
    EC2Instance:
        Condition: CreateProdResources
        Properties:
          ImageId:
            'Fn::FindInMap':
                RegionMap:
                    Ref:
                        - 'AWS::Region'
                        - AMI
        Type: 'AWS::EC2::Instance'
```
#### Resources:
- Required section
- Declare the AWS Resources such as EC2, S3 etc.
