# Ordering deploy steps: migrations, health checks and the traffic switch

Most deploy incidents come from steps running in the wrong order: migrations that break the old version, traffic switched before the new version is ready, or cleanup that removes the only working image. This is the order I use.

1. **Build and tag the image** with the commit SHA. Never deploy a tag that can change.
2. **Run tests against the built image**, not against the source tree, so you test what ships.
3. **Run backward compatible migrations.** Add columns and tables, do not drop or rename yet. The old version must keep working with the new schema.
4. **Start the new container** alongside the old one.
5. **Wait for its health check** to pass, with a timeout. If it fails, stop here and leave the old version serving.
6. **Switch traffic** at the proxy.
7. **Drain and stop the old container** after a grace period for in-flight requests.
8. **Keep the previous image** for quick rollback. Prune older ones.
9. **Run cleanup migrations later**, in a separate deploy, once nothing uses the old columns.

## Where people cut corners

- Running migrations inside the app's startup command. With two replicas they race, and a failed migration crash-loops the container.
- Skipping the health check timeout, so a stuck container blocks every future deploy.
- Deleting the old image straight away, which turns rollback into a rebuild.

## Make it a workflow

Write the steps down as a pipeline definition, so every deploy follows the same order and failures stop at a known point.

The Peon docs on [deployment workflows](https://peon.sh/docs/workflows) show how these steps can be defined. Written by a contributor to [Peon](https://peon.sh/), an open-source self-hosted deployment platform.
