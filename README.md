## **tp_turtle_regulation_Lakshana**


## Choix du gain proportionnel (Kₚ)

Lors de la Phase 1, j’ai testé trois valeurs de Kₚ pour la commande d’orientation :

- **Kₚ = 5.0** (fort)  
  - Comportement : la tortue oscille beaucoup autour de la direction cible et met plus de temps à se stabiliser.  
- **Kₚ = 0.2** (faible)  
  - Comportement : la tortue tourne très lentement, la convergence vers le waypoint est très lente.  
- **Kₚ = 1.0** (choisi)  
  - Comportement : compromis équilibré, peu d’oscillations, réponse rapide et fluide.  

  ## Régulation en distance

Pour tester l’influence de **Kₚₗ** (`kp_lin`) et du **distance_tolerance**, j’ai fait tourner le nœud **set_way_point** avec trois configurations :

| Cas              | kp_lin | distance_tolerance | Comportement observé                                                                                       |
|------------------|:------:|:------------------:|------------------------------------------------------------------------------------------------------------|
| **Kₚₗ fort**     | 1.0    | 0.1                | • `linear.x` atteignait rapidement des valeurs élevées (> 1.5), la tortue dépassait souvent le waypoint et faisait des petits allers‑retours avant de s’arrêter (oscillations).<br>• `is_moving` passait très vite à `false` mais se remettait à `true` plusieurs fois. |
| **Kₚₗ faible**   | 0.2    | 0.1                | • `linear.x` restait toujours < 0.4, la tortue avançait lentement et mettais beaucoup de temps à converger vers (7, 7).<br>• `is_moving` restait longtemps à `true`, puis passait enfin à `false` sans oscillation. |
| **Kₚₗ modéré**   | 0.5    | 0.1                | • `linear.x` montait en pic jusqu’à ~0.8, puis décroit progressivement.<br>• Convergence fluide, peu d’oscillations autour du waypoint.<br>• `is_moving` passe à `false` une seule fois, stable. |
| **Tolérance large** | 0.5 | 0.5              | • Même `kp_lin=0.5`, mais la tortue s’arrête plus tôt (dès qu’elle entre dans un cercle de rayon 0.5 autour du waypoint).<br>• `is_moving` bascule à `false` dès ~0.45 de distance.               |

> **Remarque** :  
> - Avec `kp_lin` trop grand, la tortue a du mal à freiner et oscille.  
> - Avec `kp_lin` trop faible, la convergence est monotone mais très lente.  
> - `kp_lin = 0.5` et `distance_tolerance = 0.1` offrent un compromis satisfaisant.

---

```bash
# Pour chaque essai :
# 1) Modifier set_way_point.py (kp_lin, distance_tolerance)
# 2) Rebuild & source :
colcon build --packages-select turtle_regulation
source install/setup.bash
# 3) Lancer :
ros2 run turtlesim turtlesim_node
ros2 run turtle_regulation set_way_point
ros2 topic echo /turtle1/cmd_vel
ros2 topic echo /turtle1/is_moving


