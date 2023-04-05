# 2
variable "users" {
  type = map(object({
    username = string
    groups   = list(string)
  }))
}

resource "aws_iam_user" "users" {
  for_each = var.users

  name = each.value.username
}

resource "aws_iam_group_membership" "user_groups" {
  for_each = { for user, details in var.users : user => details.groups }

  user = aws_iam_user.users[each.key].name
  groups = each.value
}

module "iam_users" {
  source = "./path/to/module"

  users = local.users
}
