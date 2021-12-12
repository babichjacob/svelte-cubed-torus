<script>
  import { spring } from "svelte/motion";

  import * as THREE from "three";
  import * as SC from "svelte-cubed";
  import { browser } from "$app/env";

  console.log(THREE.TorusKnotGeometry);

  let _size = 1;

  const sizeSlowness = 1 / 2;

  const size = spring(_size, {
    damping: Math.pow(0.1, sizeSlowness),
    stiffness: Math.pow(0.01, sizeSlowness),
    precision: 0.0004,
  });

  $: $size = _size;

  let _angle = 0;
  const angleSlowness = 1.6;
  const angle = spring(_angle, {
    damping: Math.pow(0.1, angleSlowness),
    stiffness: Math.pow(0.01, angleSlowness),
    precision: 0.0004,
  });

  if (browser) {
    setInterval(() => {
      _angle = _angle === 0 ? Math.PI : 0;
    }, angleSlowness * 1000);
  }

  $: $angle = _angle;

  const angleLagged = spring(_angle, {
    damping: 0.03,
    stiffness: 0.001,
    precision: 0.0004,
  });
  $: $angleLagged = $angle;
</script>

<div
  class="min-h-screen flex flex-col bg-gradient-to-b from-emerald-200 to-emerald-300"
>
  <div class="relative w-full flex-1">
    <SC.Canvas antialias shadows alpha>
      <SC.Mesh
        geometry={new THREE.TorusKnotGeometry(
          0.5 * $size,
          0.05 * $size,
          720,
          12,
          8,
          11
        )}
        material={new THREE.MeshStandardMaterial({
          color: 0x88ffaa,
          metalness: 0.1,
          roughness: 0.8,
        })}
        rotation={[$angle, $angle, 0]}
      />
      <SC.Mesh
        geometry={new THREE.TorusKnotGeometry(
          0.501 * $size,
          0.05 * $size + 0.002,
          720,
          12,
          8,
          11
        )}
        material={new THREE.MeshPhysicalMaterial({
          color: 0x88ffaa,
          metalness: 0.1,
          roughness: 0.5,
          envMapIntensity: 0.9,
          clearcoat: 1,
          transparent: true,
          transmission: 0.75,
          opacity: 0.99,
          reflectivity: 0.8,
          refractionRatio: 0.985,
          ior: 0.2,
          side: THREE.DoubleSide,
        })}
        rotation={[$angle, $angle, 0]}
      />
      <SC.Mesh
        geometry={new THREE.TorusKnotGeometry(
          0.501 * $size,
          0.05 * $size + 0.002,
          720,
          12,
          8,
          11
        )}
        material={new THREE.MeshPhysicalMaterial({
          metalness: 0.9,
          roughness: 0.05,
          envMapIntensity: 0.9,
          clearcoat: 1,
          transparent: true,
          transmission: 0.95,
          opacity: 0.5,
          reflectivity: 0.8,
          refractionRatio: 0.985,
          ior: 0.9,
          side: THREE.FrontSide,
          color: 0x88ffaa,
          emissive: 0x88ffaa,
          // emissiveIntensity: 0.765,
        })}
        rotation={[$angle, $angle, 0]}
      />

      <!-- <SC.Group position={[0, -height / 2 - 0.5, 0]}>
                    <SC.Mesh
                        geometry={new THREE.PlaneGeometry(50, 50)}
                        material={new THREE.MeshStandardMaterial({ color: new THREE.Color("cyan") })}
                        rotation={[-Math.PI / 2, 0, 0]}
                        receiveShadow
                    />
                </SC.Group> -->

      <!-- <SC.HemisphereLight intensity={1.0} /> -->

      <!-- <SC.SpotLight color={new THREE.Color("white")} intensity={1.0} position={[0, 1, 2]} shadow={{ mapSize: [2048, 2048] }} /> -->
      {#each { length: 12 } as _, i}
        <SC.SpotLight
          color={new THREE.Color("white")}
          intensity={0.04 * i}
          position={[0, 0.2 * i, 0]}
          shadow={{ mapSize: [2048, 2048], radius: 128 }}
        />
      {/each}

      <SC.AmbientLight intensity={1.0} />

      <SC.PerspectiveCamera
        position={[
          3 * Math.sin($angleLagged),
          2 * Math.sin($angle) - 1,
          3 * Math.cos($angleLagged),
        ]}
      />
    </SC.Canvas>
  </div>
</div>
