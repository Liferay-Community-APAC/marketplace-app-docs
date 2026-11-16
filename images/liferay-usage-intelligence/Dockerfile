# Liferay Usage Intelligence client extension zip, published as
# ghcr.io/liferay-community-apac/liferay-usage-intelligence:<version> by
# .github/workflows/liferay-usage-intelligence-image.yml from the zip in
# docs/liferay-usage-intelligence/assets/downloads/.
#
# In your own Liferay image:
#   COPY --from=ghcr.io/liferay-community-apac/liferay-usage-intelligence:<version> \
#     --chown=liferay:liferay /liferay-usage-intelligence.zip /opt/liferay/deploy/
#
# Or run it to copy the zip into a mounted deploy folder:
#   docker run --rm -v /path/to/liferay/deploy:/mnt/liferay/deploy \
#     ghcr.io/liferay-community-apac/liferay-usage-intelligence:<version>

FROM busybox:1.36.1

ARG VERSION
LABEL org.opencontainers.image.title="Liferay Usage Intelligence" \
      org.opencontainers.image.description="Liferay Usage Intelligence client extension zip, for /opt/liferay/deploy or /mnt/liferay/deploy" \
      org.opencontainers.image.source="https://github.com/Liferay-Community-APAC/marketplace-app-docs" \
      org.opencontainers.image.url="https://liferay-community-apac.github.io/marketplace-app-docs/liferay-usage-intelligence/" \
      org.opencontainers.image.version="${VERSION}"

COPY liferay-usage-intelligence.zip /liferay-usage-intelligence.zip

ENV DEPLOY_DIR=/mnt/liferay/deploy
CMD ["sh", "-c", "mkdir -p \"$DEPLOY_DIR\" && cp /liferay-usage-intelligence.zip \"$DEPLOY_DIR\"/ && echo \"Copied liferay-usage-intelligence.zip to $DEPLOY_DIR\""]
