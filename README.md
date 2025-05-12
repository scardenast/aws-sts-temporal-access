# 🛡️ Acceso temporal con roles (STS) en AWS

Este laboratorio demuestra cómo permitir que un usuario sin permisos directos acceda temporalmente a servicios de AWS (como EC2) mediante el uso de **IAM Roles** y **STS (Security Token Service)**, evitando así el uso de credenciales permanentes.

---

## 🎯 Objetivo

- Implementar acceso temporal con `sts:AssumeRole`
- Evitar el uso de credenciales permanentes
- Delegar acceso controlado a EC2 para un usuario no privilegiado

---

## 🛠️ Herramientas utilizadas

- AWS Console
- AWS IAM
- AWS CLI
- STS (Security Token Service)
- Ubuntu con WSL

---

## 📁 Estructura del repositorio

```
aws-sts-temporal-access/
├── screenshots/
│   ├── 01-crear-rol.png
│   ├── 02-relaciones-confianza.png
│   ├── 03-politica-confianza-modificada.png
│   ├── 04-assume-role-output.png
│   ├── 05-error-sin-token-y-export-correcto.png
│   └── 06-dev-temp-acceso-ec2.png
└── README.md
```

---

## 🔍 Pasos realizados

### 1️⃣ Crear el rol `DevEC2AccessRole`
Rol con permiso `AmazonEC2ReadOnlyAccess` para que sea asumido por el usuario `usuario-dev`.

📸 `screenshots/01-crear-rol.png`

---

### 2️⃣ Revisar política de confianza inicial

📸 `screenshots/02-relaciones-confianza.png`

---

### 3️⃣ Modificar política de confianza
Se permitió que el usuario `usuario-dev` asumiera el rol:

```json
"Principal": {
  "AWS": "arn:aws:iam::<ID_CUENTA>:user/usuario-dev"
}
```

📸 `screenshots/03-politica-confianza-modificada.png`

---

### 4️⃣ Usuario `usuario-dev` asume el rol con STS

```bash
aws sts assume-role \
  --role-arn arn:aws:iam::<ID_CUENTA>:role/DevEC2AccessRole \
  --role-session-name DevSession \
  --profile dev
```

📸 `screenshots/04-assume-role-output.png`

---

### 5️⃣ Error por falta de SessionToken al configurar con `aws configure`

📸 `screenshots/05-error-sin-token-y-export-correcto.png`

---

### 6️⃣ Cargar credenciales temporales correctamente con variables de entorno

```bash
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
aws ec2 describe-instances --region us-east-2
```

📸 `screenshots/06-dev-temp-acceso-ec2.png`

---

## ✅ Resultado

El usuario `usuario-dev`, sin permisos directos, logró acceder a EC2 mediante el rol temporal, cumpliendo así con buenas prácticas de seguridad en AWS.

---

## 📌 Aprendizajes

- Importancia de configurar correctamente el `SessionToken` al usar STS
- Separación entre usuarios y permisos mediante `assume-role`
- Uso adecuado de políticas de confianza en IAM

---

## 🧭 Siguiente paso sugerido

Explorar cómo aplicar MFA en la política de confianza para reforzar la seguridad del `assume-role`.
