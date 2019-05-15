# Virtual Parts Repository 2.0 (VPR2)
Virtual parts are modular and reusable mathematical models of biological parts.
Here, we describe various components of VPR2, the new version of the Virtual Parts Repository. The previous version is available at virtualparts.org. VPR2 has a Web-based repository, a Web service and a data layer. Both the Web service and the data layer can be used to connect to different synbio repositories that are enabled by the Synthetic Biology Open Language (SBOL).

<span style="color:blue">**The project is under development**</span> The following sections provides descriptions of VPR2 components and how they can be accessed.

## VPR2 Web Service
VPR2 web service is REST-based and includes several methdods that can be accessed by computational tools. The development version is available at http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi. Please see list of available methods as a Web Application Description Language (WADL) file at http://iroh.scam.keele.ac.uk/virtualparts.ws/webapi/application.wadl.

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
### Saving models
The VPR returns an object representing the model using rhe jSBML library. This model can directly be written to a file.
```java
SBMLWriter.write(sbmlDoc, "repressibleTU_Disconnected.xml",' ', (short) 2); 
```
  



