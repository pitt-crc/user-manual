# VirtualGL

## Running Interactive Graphics Jobs using VirtualGL

The traditional method of displaying graphics applications is to run a remote X server (e.g., XQuartz or Xming) on your local computer.  SSH X forwarding (also known as X11 forwarding) is an SSH protocol feature that allows you to run graphical applications on a remote server and display them on your local machine securely. This approach causes all of the OpenGL commands and 3D data to be sent over the network to be rendered on the client machine. 

While the majority of interactive graphical applications can be run without a GPU, there are a class of complex interactive visualization applications that require additional software called VirtualGL. These include OpenGL applications that utilize full 3D hardware acceleration like fsl.

VirtualGL is an open-source package that gives any Linux remote display software the ability to run OpenGL applications with full 3D hardware acceleration. With VirtualGL, the OpenGL commands and 3D data are redirected to a 3D graphics accelerator on the application server, and only the rendered 3D images are sent to the client machine. VirtualGL "virtualizes" 3D graphics hardware, allowing users to access and share large-memory visualization nodes with high-end graphics processing units (GPUs) from their energy-efficient desktops. 

Logon ondemand.htc.crc.pitt.edu, Interactive Apps -> Desktop on gpu

```
module load fsl/6.0.7.18
module load virtualgl/3.1.3 #load virtualgl
vglrun fsleyes # use vglrun <command>
```
