Para mejorar la presentación de tu README.md, aquí tienes el contenido formateado correctamente con Markdown:

markdown
# Configuración de NextAuth.js

## Paso 1: Instalar NextAuth.js
Ejecuta el siguiente comando en la raíz de tu proyecto para instalar NextAuth.js:


npm install next-auth@beta

Paso 2: Configurar variables de entorno
Crea un archivo .env.local en la raíz de tu proyecto y añade las siguientes variables:

env
AUTH_SECRET=tu_clave_secreta_generada
# Para generar una clave secreta, puedes usar:
# npx auth secret

# Configuración para el proveedor de [Google](https://x.com/i/grok?text=Google)
GOOGLE_CLIENT_ID=tu_client_id_de_google
[GOOGLE_CLIENT_SECRET](https://x.com/i/grok?text=GOOGLE_CLIENT_SECRET)=tu_client_secret_de_google

AUTH_SECRET: Es una clave secreta para cifrar tokens y cookies. Puedes generarla con el comando npx auth secret.
GOOGLE_CLIENT_ID y GOOGLE_CLIENT_SECRET: Obtén estas credenciales desde la Consola de APIs de Google.

Paso 3: Crear el archivo de configuración de NextAuth
Crea un archivo auth.ts en la raíz de tu proyecto (o dentro de una carpeta lib si prefieres organizarlo mejor):

typescript
import NextAuth from "next-auth";
import Google from "next-auth/providers/google";

export const {
  handlers: { GET, POST },
  auth,
  signIn,
  signOut,
} = NextAuth({
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    }),
  ],
  secret: process.env.AUTH_SECRET,
});

Paso 4: Configurar el Route Handler
Crea un archivo route.ts en app/api/auth/[...nextauth]/route.ts:

typescript
import { handlers } from "@/auth"; // Ajusta la ruta según la ubicación de tu archivo auth.ts
export const { GET, POST } = handlers;

Paso 5: Configurar el SessionProvider
En tu archivo app/layout.tsx, envuelve tu aplicación con el SessionProvider:

typescript
import { SessionProvider } from "next-auth/react";
import type { ReactNode } from "react";

export default function RootLayout({
  children,
}: {
  children: ReactNode;
}) {
  return (
    <html lang="es">
      <body>
        <SessionProvider>{children}</SessionProvider>
      </body>
    </html>
  );
}

Paso 6: Crear un componente de autenticación
Crea un componente AuthButton.tsx para manejar el inicio y cierre de sesión:

typescript
"use client"; // Asegúrate de que sea un componente de cliente

import { signIn, signOut } from "next-auth/react";
import { useSession } from "next-auth/react";

export default function AuthButton() {
  const { data: session } = useSession();

  return (
    <div>
      {session ? (
        <button onClick={() => signOut()}>Salir</button>
      ) : (
        <button onClick={() => signIn("google")}>Entrar con Google</button>
      )}
    </div>
  );
}

Paso 7: Usar el componente en tu aplicación
Importa y usa el componente AuthButton en tu página principal (app/page.tsx):

typescript
import AuthButton from "@/components/AuthButton";

export default function Home() {
  return (
    <main>
      <h1>Bienvenido a mi aplicación</h1>
      <AuthButton />
    </main>
  );
}

Paso 8: Proteger rutas con middleware (opcional)
Si quieres proteger rutas, crea un archivo middleware.ts en la raíz de tu proyecto:

typescript
export { default } from "next-auth/middleware";

export const config = {
  matcher: ["/dashboard"], // Protege esta ruta
};

Paso 9: Probar la aplicación
Ejecuta tu aplicación:

bash
[npm run dev](https://x.com/i/grok?text=npm%20run%20dev)

Navega a http://localhost:3000.

Haz clic en "Entrar con Google" y verifica que el inicio de sesión funcione correctamente.

Resumen de pasos
Instala NextAuth.js: npm install next-auth@beta.
Configura las variables de entorno en .env.local.
Crea el archivo de configuración auth.ts.
Configura el Route Handler en app/api/auth/[...nextauth]/route.ts.
Envuelve tu aplicación con SessionProvider en app/layout.tsx.
Crea un componente de autenticación (AuthButton.tsx).
Usa el componente en tu aplicación.
(Opcional) Protege rutas con middleware.

