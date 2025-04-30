# Publish Sockdrive bundle

Bundles with QCOW2 images are typically very large, as they include the full QCOW2 disk image. 
This makes them impractical for deployment due to their size. Additionally, since the bundle contains a block-level 
drive image, the built-in js-dos save/load functionality does not work properly. Any modification to the drive alters 
the entire image, making differential updates ineffective.

To address this, use the SockDrive protocol when deploying such drives.
