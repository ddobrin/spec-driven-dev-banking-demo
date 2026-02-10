# Spec-Driven Development - Banking Service Demo

> [!IMPORTANT]
> 
> This is a set of simple demos to illustrate a specification-first implementation of a Banking Customer Information Facility (CIF) service
> 
> It represents a fictitious example, not an official specification! 

### Generating a Service from a SPEC

Follow the instructions in the [README.md](banking-cif-service-gen/README.md).

Available files: 

* [INSTALL.md](banking-cif-service-gen/INSTALL.md): Instructions for building banking-cif-service-gen, for you, the human.
* [SPEC.md](banking-cif-service-gen/SPEC.md): A detailed description of how the service should behave and how it should be implemented.
* [tests.yaml](banking-cif-service-gen/test.yaml): A list of language-agnostic test cases, defined as input/output pairs, that any implementation must pass.
* [ddl.md](banking-cif-service-gen/ddl.md): The data model description for Person, Products and Transactions
* [TECHSTACK.md](banking-cif-service-gen/TECHSTACK.md): An example techstack to be used when generating the code

### End-to-end example

> [!NOTE] 
> 
> You can follow an AI assisted E2E example focused on:
> 
> * Generate DDL from prompt
> * Generate SPEC from prompt + DDL
> * Generate app from prompt + SPEC + DDL

To build the banking-cif-service with only specs and prompts, follow the path:

1. Generate a DDL using the following [Prompt](ddl-gen/ddl-gen.md).

2. Copy the generated ddl.md file to the [spec-gen](spec-gen) folder and generate a specification from the DDL and [Prompt](spec-gen/spec-gen.md)

3. Follow the instructions in the generated INSTALL.md file and use a Techstack instruction, for example [TECHSTACK.md](banking-cif-service-gen/TECHSTACK.md)