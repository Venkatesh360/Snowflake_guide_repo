# Custom Roles in Snowflake

## Overview
Custom roles in Snowflake allow organizations to define specific access controls tailored to their security and operational needs. Unlike system-defined roles, custom roles provide flexibility in managing privileges for different users and teams.

## Key Characteristics
- Custom roles can be created and assigned privileges based on business requirements.
- They can be structured in a hierarchy, inheriting privileges from other roles.
- Typically, custom roles are assigned under the SYSADMIN role for best practice.
- They help implement the principle of least privilege, ensuring users only have the necessary access.

## Creating a Custom Role
To create a custom role in Snowflake, use the following SQL command:
```sql
CREATE ROLE custom_role_name;
```

## Assigning Privileges to a Custom Role
Once a role is created, privileges can be granted to it:
```sql
GRANT SELECT ON TABLE my_table TO ROLE custom_role_name;
```

## Assigning a Role to a User
Users can be assigned to a role using:
```sql
GRANT ROLE custom_role_name TO USER user_name;
```

## Managing Role Hierarchies
Custom roles can inherit privileges from other roles:
```sql
GRANT ROLE custom_role_name TO ROLE sysadmin;
```

## Limitations
- Custom roles do not have privileges by default; all access must be explicitly granted.
- They cannot modify system-defined roles.
- The role hierarchy should be carefully designed to avoid excessive privilege inheritance.

## Best Practices
- Assign privileges only as needed to avoid unnecessary access.
- Use role hierarchies effectively to streamline access control.
- Regularly audit roles to ensure security compliance.

By using custom roles strategically, organizations can enhance security while maintaining operational efficiency in Snowflake.

