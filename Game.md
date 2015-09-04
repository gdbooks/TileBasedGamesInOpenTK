#Game (Skeleton)

In case the code repo ever goes down, this is a local backup of the **Game** class

```cs
using System;
using GameFramework;
using System.Collections.Generic;

namespace Skeleton {
    class Game {
        // Singleton
        private static Game instance = null;
        public static Game Instance {
            get {
                if (instance == null) {
                    instance = new Game();
                }
                return instance;
            }
        }

        protected Game() {

        }

        public void Initialize() {

        }

        public void Update(float dt) {

        }

        public void Render() {

        }

        public void Shutdown() {

        }
    }
}

```