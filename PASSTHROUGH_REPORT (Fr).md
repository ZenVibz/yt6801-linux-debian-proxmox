Rapport d'Analyse : Échec du PCI Passthrough - Motorcomm YT6801
1. Contexte du projet
Hôte : Mini PC Ninkear N10PRO N150 (Architecture Intel Twin Lake).

OS Hôte : Proxmox VE 8.x (Kernel 6.17+).

Périphérique cible : Motorcomm Microelectronics YT6801 Gigabit Ethernet Controller (ID 1f0a:6801).

Objectif : Isoler le contrôleur réseau physique pour le dédier exclusivement à une machine virtuelle Debian 13 (Sandbox) via l'IOMMU.

2. Spécifications Matérielles
CPU : Intel N150.

IOMMU : Activé (Groupes IOMMU segmentés avec succès).

Isolation : Le contrôleur YT6801 réside seul dans le groupe IOMMU 15 (Adresse 01:00.0), ce qui est théoriquement idéal pour le passthrough.

3. Méthodologie et Tentatives de Résolution
A. Configuration du Socle (Hôte)
L'activation de l'IOMMU et l'isolation via le pilote vfio-pci ont été réalisées avec succès :

Paramètres Kernel : intel_iommu=on iommu=pt.

Réservation VFIO : options vfio-pci ids=1f0a:6801.

Vérification : Kernel driver in use: vfio-pci confirmé sur l'hôte.

B. Optimisations de Gestion d'Énergie (ASPM/D3)
Face à l'impossibilité de la VM d'initialiser la carte, plusieurs couches de désactivation de l'économie d'énergie ont été testées pour forcer l'état de puissance D0 (Actif) :

Désactivation globale de l'ASPM : pcie_aspm=off.

Désactivation de la gestion d'énergie des ports PCIe : pcie_port_pm=off.

Forçage du maintien en éveil via VFIO : disable_idle_d3=1.

C. Gestion des Erreurs et Reset
Pour contrer les plantages du bus PCIe lors de la réinitialisation de la VM :

Désactivation de l'AER (Advanced Error Reporting) : pci=noaer.

Désactivation du reset matériel et du ROM-Bar au démarrage de la VM.

4. Analyse des Blocages (Logs dmesg)
Malgré toutes les optimisations, les logs système rapportent une erreur fatale récurrente :

Plaintext
vfio-pci 0000:01:00.0: Unable to change power state from D3cold to D0, device inaccessible
pcieport 0000:00:1c.0: broken device, retraining non-functional downstream link at 2.5GT/s
vfio-pci 0000:01:00.0: not ready 65535ms after bus reset; giving up
Diagnostic Technique :
Instabilité Électrique du Pont PCIe : Le pont PCIe (00:1c.0) perd la synchronisation physique avec le contrôleur Motorcomm dès que le pilote VFIO tente un cycle de réinitialisation (Bus Reset).

Incompatibilité Firmware/Matériel : Le contrôleur YT6801, tel qu'implémenté sur cette carte mère N150, semble incapable de sortir de l'état de veille profonde D3cold sans une initialisation complète par le BIOS ou un pilote hôte spécifique.

Lien Downstream Brisé : L'impossibilité de "retraining" du lien indique que le matériel ne répond plus électriquement au bus PCIe après la tentative de capture par la VM.

5. Conclusion
Le PCI Passthrough direct du contrôleur Motorcomm YT6801 sur l'architecture Ninkear N150 est jugé impossible avec les versions actuelles du noyau et du firmware. Le matériel ne supporte pas les exigences de réinitialisation et de changement d'état de puissance (Power States) nécessaires à la virtualisation matérielle via IOMMU.

Acknowledgments
Toutes les recherches techniques, diagnostics de logs et tentatives d'optimisation kernel présentés dans ce rapport ont été réalisés en collaboration avec Gemini AI de Google. Cette assistance a permis d'explorer les limites de la virtualisation sur l'architecture Intel N-series et de valider les contraintes matérielles du contrôleur YT6801.
