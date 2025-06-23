# How to Write a ConfigMap YAML to Store Database Connection Settings in Kubernetes

When working with Kubernetes, managing application configuration properly is super important. One of the best ways to do this is by using a **ConfigMap**. If you want to store your database connection settings like host, port, username, and database name, ConfigMaps help keep these details separate from your application code. This makes your app more flexible, secure, and easier to manage.

In this article, I will guide you step-by-step on how to write a **ConfigMap YAML** file that stores your database connection settings. I’ll also show you how to use it in your Kubernetes cluster, so your app can access those settings easily.

---

## What Is a ConfigMap in Kubernetes?

First, let’s understand what a ConfigMap is.

A **ConfigMap** is a Kubernetes object that lets you store non-confidential data in key-value pairs. This means you can keep configuration settings outside your container images. When your app runs inside Kubernetes, it can read these values from the ConfigMap without needing to rebuild or change the app.

Think of ConfigMaps as a way to inject configuration into your app at runtime — so you don’t have to hardcode important details like database host or port inside your app code.

---

## Why Store Database Connection Settings in ConfigMaps?

Database connection settings are critical. They tell your application where to find the database and how to connect. Typical settings include:

* **DB\_HOST** — The database server address
* **DB\_PORT** — The port number to connect on
* **DB\_USER** — Username to log in
* **DB\_PASSWORD** — Password for authentication
* **DB\_NAME** — The specific database name

Storing these settings in a ConfigMap keeps your app flexible. You can change connection details anytime without changing your application code. This also helps when you have different environments like development, testing, and production with different database servers.

**Note:** Passwords and other sensitive data should ideally be stored in Kubernetes **Secrets** instead of ConfigMaps, as ConfigMaps are not encrypted.

---

## Basic Structure of a ConfigMap YAML File

A ConfigMap is written in YAML format. It has a simple structure with a few key parts:

* `apiVersion`: The Kubernetes API version (usually `v1`)
* `kind`: Always `ConfigMap`
* `metadata`: Information about the ConfigMap like its name and namespace
* `data`: The actual key-value pairs containing your configuration

---

## Step-by-Step: Writing a ConfigMap YAML for Database Connection

Let me show you how to write the YAML file.

### 1. Define Metadata

Start with naming your ConfigMap. The name should be unique within a namespace.

```yaml
metadata:
  name: db-connection-config
  namespace: default
```

If you don’t specify a namespace, it defaults to `default`.

### 2. Add Data Section

Under the `data` section, add your database connection parameters as key-value pairs.

```yaml
data:
  DB_HOST: "mysql.example.com"
  DB_PORT: "3306"
  DB_USER: "admin"
  DB_PASSWORD: "securepassword"
  DB_NAME: "mydatabase"
```

Note that all values are strings in YAML.

### 3. Combine Everything

Here’s the full example of a ConfigMap YAML for database connection settings:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: db-connection-config
  namespace: default
data:
  DB_HOST: "mysql.example.com"
  DB_PORT: "3306"
  DB_USER: "admin"
  DB_PASSWORD: "securepassword"
  DB_NAME: "mydatabase"
```

---

## How to Apply This ConfigMap

Once your YAML file is ready (for example, save it as `db-config.yaml`), apply it to your Kubernetes cluster using this command:

```bash
kubectl apply -f db-config.yaml
```

You should see a confirmation like:

```
configmap/db-connection-config created
```

To check if your ConfigMap exists, run:

```bash
kubectl get configmaps
```

---

## How Your Application Can Use This ConfigMap

There are two common ways your app can read the ConfigMap data inside Kubernetes:

### 1. Environment Variables

You can inject ConfigMap keys as environment variables inside your pod. Here is an example snippet from a Deployment YAML:

```yaml
envFrom:
  - configMapRef:
      name: db-connection-config
```

This injects all key-value pairs as environment variables your app can use.

### 2. Mounted as Files

Alternatively, you can mount the ConfigMap as files inside the container. Each key becomes a file containing its value.

---

## Best Practices to Remember

* **Never store sensitive info like passwords in ConfigMaps. Use Kubernetes Secrets instead.**
* Keep your ConfigMaps simple and focused on configuration only.
* Use meaningful names for your ConfigMaps to avoid confusion.
* Use version control to track your ConfigMap YAML files.
* Always test your ConfigMap changes on a development cluster first.

---

## Troubleshooting Tips

If your application does not get the config values:

* Check if the ConfigMap is created properly with `kubectl describe configmap db-connection-config`
* Ensure your pod’s Deployment references the ConfigMap correctly
* Verify there are no YAML syntax errors or typos
* Check pod logs to see if environment variables are set

---

## Conclusion

Managing database connection settings with ConfigMaps is a clean, easy, and powerful way to make your Kubernetes applications flexible. By separating configuration from code, you can update your settings anytime without rebuilding containers. Remember to store sensitive info securely using Secrets.

Next time you deploy a database-backed app, give ConfigMaps a try — it will save you a lot of headaches and make your deployments much smoother.

---

If you want help creating Deployment YAML to use this ConfigMap or need examples for mounting ConfigMaps, just ask. I’m here to help!
