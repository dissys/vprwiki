# Virtual Parts Repository 2.0 (VPR2)
Virtual parts are modular and reusable mathematical models of biological parts.
Here, we describe various components of VPR2, the new version of the Virtual Parts Repository. The previous version is available at virtualparts.org. VPR2 has a Web-based repository, a Web service and a data layer. Both the Web service and the data layer can be used to connect to different synbio repositories that are enabled by the Synthetic Biology Open Language (SBOL).

<span style="color:blue">**The project is under development**</span> The following sections provides descriptions of VPR2 components and how they can be accessed.

## VPR2 Web Service
VPR2 web service is REST-based and includes several methdods that can be accessed by computational tools. The development version is available at [http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi](http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi). Please see list of available methods as a Web Application Description Language (WADL) file at [http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi/application.wadl](http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi/application.wadl).

## VPR2 Web Service Client
Although, the Web service can directly be accessed by tools, we also provide a Java client library to facilitate programmatic access. The client can be downloaded from 

## VPR2 Repository

## Using the VPR2 Web Service Client
The Web service can be used to create models directly from SBOL files. Basic information about designs in the form of SBOL documents can also be used to retreve detailed information from a specified repository.

### Using the library in disconnected mode
This option allows users to specify genetic circuits using SBOL to create models based on the constraints specified. The SBOL designs should include all the biological interactions, except degradation reactions which are added automatically by the VPR2.

The following code creates an SBML model using an SBOL design as a parameter. The SBML model is specified in the SBML Level 3 format which is the latest SBML version and supports hierarchical models.

```java
WebTarget target = VPRWebServiceClient.getVPRWebServiceTarget(VPR_WS);
SBMLDocument sbmlDoc = VPRWebServiceClient.getModel(target, sbolDesign);
```

### Using the library in connected mode
This option allows users to specify basic information about about genetic circuit design and retrive an SBML model using the information from a remote data repository, which may be the default VPR repository or another SBOL enabled repository such as a SynBioHub instance.

The following code creates an SBML model using an SBOL design as a parameter. An additional parameter is also used to specify the URI of the SPARQL interface of the remote repository.
```java
WebTarget target=VPRWebServiceClient.getVPRWebServiceTarget(VPR_WS);
SBOLDocument sbolDesign=SVPWriteHandler.convertToSBOL(design,"tu");    	    	    	
SBMLDocument sbmlDoc=VPRWebServiceClient.getModelUsingDataFromStack(target, sbolDesign, VPR_Repository);    	
```

### Creating SBOL designs
SBOL designs can be created using different SBOL tools. The libSBOLj java library is included as part of the VPR2 and can be used to create SBOL designs. Alternatively, simple designs can be constructed via the SVPWrite language using the following notation. Note that each part id-type pair MUST be separated by a semicolon. The LAST pair MUST NOT be followed by a semicolon.
```
<partid>:<parttype>
```

Part types in SVPWrite are:
* prom : Promoter
* cds : CDS
* rbs :RBS
* ter : Terminator
* op :Operator
* shim : Spacer
* eng : Engineered Region

The following example creates the prom1-rbs1-cds1-ter1 design where prom1 is a promoter, rbs1 is an RBS, cds1 is a CDS and ter1 is a terminator.
```java
String design="prom1:prom;rbs1:rbs;cds1:cds;ter1:ter";
SBOLDocument sbolDesign=SVPWriteHandler.convertToSBOL(design,"tu");    
```

SBOL equivalents of newly created design components can be referred to directly from the SBOL documents as shown below. The cds1 component is retrieved back as an SBOL ComponentDefinition object, which can then be used to define interactions.

```java
ComponentDefinition cdsCompDef=sbolDesign.getComponentDefinition(URI.create(sbolDesign.getDefaultURIprefix() + "cds1"));  
```

### Adding information about biological interactions
In order to create interactions in the disconnected mode, interactions must be fully specified. Interactions MUST be created using the interaction types presented in [https://pubs.acs.org/doi/abs/10.1021/acssynbio.7b00459](https://pubs.acs.org/doi/abs/10.1021/acssynbio.7b00459). The VPR provides SBOLInteraction class to guarantee that the interactions are created correctly. Interactions must be created as part of a single SBOL ModuleDefinition which is the container entity for biological interactions. The VPR library supports the following interactions.

* Promoter repression
* Promoter activation
* Protein-DNA binding
* Complex formation between biological molecules
* Phosphorylation
* Auto dephosphorylation

The example below creates a ModuleDefinion as a container store information about interactions. Next, it creates two interactions. The first interaction represents the translation of the LacI protein from the lacI coding sequence. The second interaction represents the repression of the pTac promoter by the LacI protein.

```java
ModuleDefinition moduleDef=sbolDesign.createModuleDefinition("design_module");
SBOLInteraction.createTranslationInteraction(moduleDef, lacI, LacI);
SBOLInteraction.createPromoterRepression(moduleDef, pTac, LacI);
```

### Controlling kinetic rate parameters
The VPR library populates SBML models with default values. These values can also be controlled by the user. Often, parameters are properties of interaction objects. For example, the following code creates a repression interaction. The forward rate of this interaction can be overwritten by the user. In the example, the new value is set to 0.1.
```java
Interaction repression=SBOLInteraction.createPromoterRepression(moduleDef, pTac, LacI);
repression.createAnnotation(VPRTerms.toQName(VPRTerms.parameter.kforward,VPRTerms.Ns.getPrefix()), "0.1"); 
```

### Saving models
The VPR returns an object representing the model using rhe jSBML library. This model can directly be written to a file.
```java
SBMLWriter.write(sbmlDoc, "repressibleTU_Disconnected.xml",' ', (short) 2); 
```
  


