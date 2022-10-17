# System Configuration
All of the system configuration for Guix is maintained in a scheme file kept in your home directory `~/.config/guix/system.scm` here, you will set system configuration items like service settings, partitioning, locale, networking and that sort of stuff.

There is a great breakdown of all of the variables you can define for the system configuration in the 

A pretty vanilla one just after install might look something like this
```scheme
;; This is an operating system configuration template ;; for a "bare bones" setup, with no X11 display server. 
([use-modules](https://www.gnu.org/software/guile/manual/html_node/Using-Guile-Modules.html#index-use_002dmodules) (gnu))
(use-service-modules networking ssh)
(use-package-modules screen ssh)

([operating-system](https://guix.gnu.org/manual/en/html_node/operating_002dsystem-Reference.html#index-operating_002dsystem-1)
  (host-name "komputilo")
  (timezone "Europe/Berlin")
  (locale "en_US.utf8")

  ;; Boot in "legacy" BIOS mode, assuming /dev/sdX is the
  ;; target hard disk, and "my-root" is the label of the target
  ;; root file system.
  (bootloader ([bootloader-configuration](https://guix.gnu.org/manual/en/html_node/Bootloader-Configuration.html#index-bootloader_002dconfiguration)
                (bootloader grub-bootloader)
                (target "/dev/sdX")))
  (file-systems ([cons](https://www.gnu.org/software/guile/manual/html_node/Pairs.html#index-cons) ([file-system](https://guix.gnu.org/manual/en/html_node/File-Systems.html#index-file_002dsystem)
                        (device ([file-system-label](https://guix.gnu.org/manual/en/html_node/File-Systems.html#index-file_002dsystem_002dlabel-1) "my-root"))
                        (mount-point "/")
                        (type "ext4"))
                      [%base-file-systems](https://guix.gnu.org/manual/en/html_node/File-Systems.html#index-_0025base_002dfile_002dsystems)))

  ;; This is where user accounts are specified.  The "root"
  ;; account is implicit, and is initially created with the
  ;; empty password.
  (users ([cons](https://www.gnu.org/software/guile/manual/html_node/Pairs.html#index-cons) ([user-account](https://guix.gnu.org/manual/en/html_node/User-Accounts.html#index-user_002daccount)
                (name "alice")
                (comment "Bob's sister")
                (group "users")

                ;; Adding the account to the "wheel" group
                ;; makes it a sudoer.  Adding it to "audio"
                ;; and "video" allows the user to play sound
                ;; and access the webcam.
                (supplementary-groups '("wheel"
                                        "audio" "video")))
               [%base-user-accounts](https://guix.gnu.org/manual/en/html_node/User-Accounts.html#index-_0025base_002duser_002daccounts)))

  ;; Globally-installed packages.
  (packages ([cons](https://www.gnu.org/software/guile/manual/html_node/Pairs.html#index-cons) screen %base-packages))

  ;; Add services to the baseline: a DHCP client and
  ;; an SSH server.
  (services ([append](https://www.gnu.org/software/guile/manual/html_node/Append_002fReverse.html#index-append) ([list](https://www.gnu.org/software/guile/manual/html_node/List-Constructors.html#index-list-1) ([service](https://guix.gnu.org/manual/en/html_node/Service-Reference.html#index-service) [dhcp-client-service-type](https://guix.gnu.org/manual/en/html_node/Networking-Services.html#index-dhcp_002dclient_002dservice_002dtype))
                          ([service](https://guix.gnu.org/manual/en/html_node/Service-Reference.html#index-service) [openssh-service-type](https://guix.gnu.org/manual/en/html_node/Networking-Services.html#index-openssh_002dservice_002dtype)
                                   ([openssh-configuration](https://guix.gnu.org/manual/en/html_node/Networking-Services.html#index-openssh_002dconfiguration)
                                    (openssh openssh-sans-x)
                                    (port-number 2222))))
                    [%base-services](https://guix.gnu.org/manual/en/html_node/Base-Services.html#index-_0025base_002dservices-1))))
```

The choices for variable names and keywords involved in the system configuration were chosen so that your scheme file could be as self-describing as possible. You might augment it with a few comments but  you shouldn't need to over-do it here. 

# Package Management
