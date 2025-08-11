
```bash
#!/bin/bash

  

set -e


APP_NAME="CpuMonitorApp"
VERSION="1.0.0"
RUNTIME="linux-arm64"
ARCH="arm64"
MAINTAINER="Gideon Purwoko Adityas <gideon.adityas@formulatrix.com>"
PROJECT_DIR="."
RELEASE_DIR_BARE="${PROJECT_DIR}/release"
RELEASE_DIR="${RELEASE_DIR_BARE}/${APP_NAME}_${VERSION}"
PUBLISH_DIR="${PROJECT_DIR}/publish"
SERVICE_NAME=$(echo "$APP_NAME" | tr '[:upper:]' '[:lower:]')

#echo "[1/9] Publishing .NET project..."
dotnet clean ${PROJECT_DIR}/${APP_NAME}.csproj
dotnet publish ${PROJECT_DIR}/${APP_NAME}.csproj -c Release -r $RUNTIME --self-contained true -o "$PUBLISH_DIR"

#echo "[2/9] Preparing package directory structure..."
mkdir -p "$RELEASE_DIR/DEBIAN"
mkdir -p "$RELEASE_DIR/opt/$APP_NAME"
mkdir -p "$RELEASE_DIR/etc/systemd/system"
mkdir -p "$RELEASE_DIR/usr/bin"


cat > "$RELEASE_DIR/DEBIAN/control" <<EOF
Package: ${SERVICE_NAME}
Version: ${VERSION}
Section: base
Priority: optional
Architecture: ${ARCH}
Maintainer: ${MAINTAINER}
Description: ${APP_NAME} service packaged as .deb
EOF


cat > "$RELEASE_DIR/etc/systemd/system/${SERVICE_NAME}.service" <<EOF
[Unit]
Description=${APP_NAME} service
[Service]
WorkingDirectory=/opt/${APP_NAME}
ExecStart=/opt/${APP_NAME}/${APP_NAME}
Restart=always
RestartSec=10
KillSignal=SIGINT
SysLogIdentifier=${SERVICE_NAME}
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
EOF

  
cat > "$RELEASE_DIR/DEBIAN/postinst" <<EOF

#!/bin/bash

set -e

systemctl daemon-reexec || true
systemctl daemon-reload
systemctl enable ${SERVICE_NAME}.service
systemctl start ${SERVICE_NAME}.service
EOF

chmod 755 "$RELEASE_DIR/DEBIAN/postinst"

cat > "$RELEASE_DIR/DEBIAN/prerm" <<EOF
#!/bin/bash
set -e

systemctl stop ${SERVICE_NAME}.service || true
systemctl disable ${SERVICE_NAME}.service || true
EOF

chmod 755 "$RELEASE_DIR/DEBIAN/prerm"

cat > "$RELEASE_DIR/DEBIAN/postrm" <<EOF
#!/bin/bash
set -e

if [ -f /etc/systemd/system/${SERVICE_NAME}.service ]; then
	rm -f /etc/systemd/system/${SERVICE_NAME}.service\
	systemctl daemon-reload
fi

EOF

chmod 755 "$RELEASE_DIR/DEBIAN/postrm"  

cat > "$RELEASE_DIR/usr/bin/${SERVICE_NAME}" <<EOF
#!/bin/sh
/opt/${APP_NAME}/${APP_NAME}
EOF

chmod +x "$RELEASE_DIR/usr/bin/${SERVICE_NAME}"

# echo "[9/9] Copying published files..."
cp -r "$PUBLISH_DIR"/. "$RELEASE_DIR/opt/$APP_NAME"

echo "Building .deb package..."
#cd release
dpkg-deb --build --root-owner-group "${RELEASE_DIR_BARE}/${APP_NAME}_${VERSION}"
#cd ..

echo "Done! .deb package created at: release/${APP_NAME}_${VERSION}.deb"
```