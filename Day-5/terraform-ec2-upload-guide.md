
# 🚀 Terraform EC2 Instance with Key Pair and Python File Upload

This guide walks through generating an SSH key pair, uploading a Python file to an EC2 instance using Terraform, and connecting from your local machine to verify the upload.

---

## 🔐 1. Generate SSH Key Pair (Windows PowerShell)

Use the following command in **PowerShell** to generate a 4096-bit RSA key pair:

```powershell
ssh-keygen -t rsa -b 4096 -f "C:\Users\sures\OneDrive\Desktop\Devops notes\terrafom practice\SSH key_pair\my_terraform_key"
```

- This creates:
  - A **private key**: `my_terraform_key`
  - A **public key**: `my_terraform_key.pub`
- 🔸 **Note**: The private key doesn't need to have a `.pem` extension — it's still valid.

---

## 🧩 2. Use Keys in Terraform

- In the Terraform `key_pair` resource, specify the **public key** path.
- In the `connection` block (for `file` or `remote-exec` provisioners), specify the **private key** path.

### ✅ Example (in `main.tf`):

```hcl
resource "aws_key_pair" "my_key" {
  key_name   = "my_terraform_key"
  public_key = file("C:/Users/sures/OneDrive/Desktop/Devops notes/terrafom practice/SSH key_pair/my_terraform_key.pub")
}

connection {
  type        = "ssh"
  user        = "ec2-user"
  private_key = file("C:/Users/sures/OneDrive/Desktop/Devops notes/terrafom practice/SSH key_pair/my_terraform_key")
  host        = self.public_ip
}
```

---

## 📁 3. Upload Python File Using `file` Provisioner

To upload a file (e.g., `hello.py`) to the EC2 instance, use the `file` provisioner:

```hcl
provisioner "file" {
  source      = "hello.py"
  destination = "/home/ec2-user/hello.py"
}
```

- This requires the private key to establish the SSH connection.
- 🔸 By default, Amazon Linux comes with Python pre-installed — no need to install it again using `remote-exec`.

---

## 🌐 4. Connect to EC2 from Local Machine

After the instance is up and the file is uploaded:

Run the following command in PowerShell (replace `<PUBLIC_IP>` with your EC2 instance's public IP):

```powershell
ssh -i "C:\Users\sures\OneDrive\Desktop\Devops notes\terrafom practice\SSH key_pair\my_terraform_key" ec2-user@<PUBLIC_IP>
```

---

## 🧪 5. Verify Uploaded File

Once connected to the instance:

- List files in the home directory:

```bash
ls
```

- Display the contents of the Python file:

```bash
cat hello.py
```

---

## 📌 Summary

| Task | Tool/Command |
|------|--------------|
| Generate key pair | `ssh-keygen` |
| Reference public key in Terraform | `aws_key_pair` resource |
| Upload Python file | `file` provisioner |
| Connect to EC2 | `ssh -i ... ec2-user@<IP>` |
| Check uploaded file | `ls`, `cat` |

---

## ✅ Notes

- The **public key** is uploaded to AWS during EC2 creation.
- The **private key** stays local and is only used in:
  - Terraform `connection` blocks
  - SSH commands from your machine
- Python is already available on Amazon Linux 2.

---

## 📂 Suggested File Structure

```
terraform-ec2-upload/
│
├── main.tf
├── hello.py
└── SSH key_pair/
    ├── my_terraform_key
    └── my_terraform_key.pub
```
