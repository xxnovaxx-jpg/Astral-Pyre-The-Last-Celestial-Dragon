# Astral-Pyre-The-Last-Celestial-Dragon
Where stars die, dragons rise.  In the twilight of a fading universe, a cosmic dragon awakens — forged from starlight and born of the void. Soar through shattered constellations, unleash celestial fire, and reignite the dying cosmos.  Become the flame that burns eternity.







public class DragonController : MonoBehaviour
{
    [Header("Movement Settings")]
    public float moveSpeed = 15f;
    public float lookSensitivity = 3f;
    public float ascendSpeed = 10f;

    [Header("Fireball Settings")]
    public GameObject fireballPrefab;
    public Transform firePoint;
    public float fireballForce = 1000f;
    public float fireRate = 0.5f;

    private Rigidbody rb;
    private Camera playerCam;
    private float nextFireTime = 0f;
    private float rotationX = 0f;
    private float rotationY = 0f;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
        playerCam = GetComponentInChildren<Camera>();
        Cursor.lockState = CursorLockMode.Locked;
        Cursor.visible = false;
    }

    void Update()
    {
        HandleLook();
        HandleShoot();
    }

    void FixedUpdate()
    {
        HandleMovement();
    }

    void HandleMovement()
    {
        float moveX = Input.GetAxis("Horizontal");
        float moveZ = Input.GetAxis("Vertical");
        float moveY = 0f;

        if (Input.GetKey(KeyCode.Space)) moveY = 1f;
        else if (Input.GetKey(KeyCode.LeftControl)) moveY = -1f;

        Vector3 moveDir = transform.forward * moveZ + transform.right * moveX + transform.up * moveY;
        rb.velocity = moveDir * moveSpeed;
    }

    void HandleLook()
    {
        float mouseX = Input.GetAxis("Mouse X") * lookSensitivity;
        float mouseY = Input.GetAxis("Mouse Y") * lookSensitivity;

        rotationY += mouseX;
        rotationX -= mouseY;
        rotationX = Mathf.Clamp(rotationX, -80f, 80f);

        transform.localRotation = Quaternion.Euler(0, rotationY, 0);
        playerCam.transform.localRotation = Quaternion.Euler(rotationX, 0, 0);
    }

    void HandleShoot()
    {
        if (Input.GetKey(KeyCode.Z) && Time.time >= nextFireTime)
        {
            nextFireTime = Time.time + fireRate;
            ShootFireball();
        }
    }

    void ShootFireball()
    {
        if (fireballPrefab == null || firePoint == null)
        {
            Debug.LogWarning("Missing fireball prefab or firePoint.");
            return;
        }

        GameObject fireball = Instantiate(fireballPrefab, firePoint.position, firePoint.rotation);
        Rigidbody rbFireball = fireball.GetComponent<Rigidbody>();
        rbFireball.AddForce(playerCam.transform.forward * fireballForce);

        Destroy(fireball, 5f);
    }
}
using UnityEngine;

public class Fireball : MonoBehaviour
{
    public float damage = 10f;
    public GameObject explosionEffect;

    void OnTriggerEnter(Collider other)
    {
        // Later, this will damage aliens or break cages
        if (other.CompareTag("Alien"))
        {
            // Placeholder for enemy damage
            Debug.Log("Hit alien!");
        }

        if (explosionEffect != null)
            Instantiate(explosionEffect, transform.position, Quaternion.identity);

        Destroy(gameObject);
    }
}
