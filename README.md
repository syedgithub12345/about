+++Hosting Your Resume on AWS EC2 with a CI/CD Setup Using GitHub Actions+++

1. Create an EC2 instance, here ubuntu Linux with the required security group with inbound rules 443,80,443 port range.

2. Upload your resume to GitHub.

3. On GitHub settings-> actions-> create new secret repository by entering the name: EC2_SSH_KEY and secret of the .pem file.

4. Create a new secret repo, name: HOST_DNS and public IP domain.

5. Create a new secret repo, name: USERNAME and secret: Ubuntu.

6. Create a new secret repo, name: TARGET_DIR and secret: home.

7. Now your github-actions-ec2.yml should be present in .github/workflows/github-actions-ec2.yml in your repository

8. Paste the code in the .yml file
***********************************************************
name: Push-to-EC2

# Trigger deployment only on push to main branch
on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy to EC2 on master branch push
    runs-on: ubuntu-latest

    steps:
      - name: Checkout the files
        uses: actions/checkout@v2

      - name: Deploy to Server 1
        uses: easingthemes/ssh-deploy@main
        env:
          SSH_PRIVATE_KEY: ${{ secrets.EC2_SSH_KEY }}
          REMOTE_HOST: ${{ secrets.HOST_DNS }}
          REMOTE_USER: ${{ secrets.USERNAME }}
          TARGET: ${{ secrets.TARGET_DIR }}

      - name: Executing remote ssh commands using ssh key
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST_DNS }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            sudo apt-get -y update
            sudo apt-get install -y apache2
            sudo systemctl start apache2
            sudo systemctl enable apache2
            cd home
            sudo mv * /var/www/html
**************************************************************

9. Deployment successful.
