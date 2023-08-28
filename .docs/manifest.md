# :scroll: [Manifest](https://kubernetes.io/docs/reference/glossary/?all=true#term-manifest)

Specifying a Kubernetes API object in JSON or YAML format.

A manifest specifies the desired state of an object that Kubernetes will maintain when you apply the manifest. Each configuration file can contain multiple manifests.

:round_pushpin: Using the manifest with a [here document](https://en.wikipedia.org/wiki/Here_document) -- with `EOF` (End Of File) separator

```yaml
$ kubectl apply --filename - <<EOF

The YAML file goes into this space separated by the EOF separator

---
EOF
```

:round_pushpin: Using the manifest with a `.yaml` file

```yaml
kubectl apply --filename monfichier.yaml
```

:round_pushpin: Using the manifest with a URL

```yaml
kubectl apply --filename https://myserver.com/myfile.yaml
```

# [:back:](../#control_knobs-the-planes) :angry: I knew that already

# References

- [ ] [Creating objects](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#creating-objects)

